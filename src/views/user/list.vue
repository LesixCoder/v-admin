<template>
  <div class="user-list">
    <a-table
      :columns="columns" 
      :dataSource="data"
      :pagination="pagination"
      :loading="loading"
      :rowKey="record => record.login.uuid"
      @change="handleTableChange"
    >
      <template 
        v-for="col in ['name', 'gender', 'email']" 
        :slot="col" 
        slot-scope="text, record, index"
      >
        <div :key="col">
          <a-input
            v-if="col === 'name' && record.editable"
            style="margin: -5px 0"
            :value="record.name.first + ' ' +record.name.last"
            @change="e => handleChange(e.target.value, record.login.uuid, col)"
          />
          <a-input
            v-else-if="record.editable"
            style="margin: -5px 0"
            :value="text"
            @change="e => handleChange(e.target.value, record.login.uuid, col)"
          />
          <template v-else>
            <span v-if="col == 'name'">{{ text.first + ' ' + text.last  }}</span>
            <span v-else-if="col == 'gender'">{{text == 'male' ? '男' : '女'}}</span>
            <span v-else>{{text}}</span>
          </template>
        </div>
      </template>
      <template slot="operation" slot-scope="text, record, index">
        <div class='editable-row-operations'>
          <span v-if="record.editable">
            <a @click="() => save(record.login.uuid)">Save</a>
            <a-popconfirm title='Sure to cancel?' @confirm="() => cancel(record.login.uuid)">
              <a>Cancel</a>
            </a-popconfirm>
          </span>
          <span v-else>
            <a @click="() => edit(record.login.uuid)">Edit</a>
          </span>
        </div>
      </template>
    </a-table>
  </div>
</template>

<script>
const columns = [{
  title: '姓名',
  dataIndex: 'name',
  sorter: true,
  width: '20%',
  scopedSlots: { customRender: 'name' },
}, {
  title: '性别',
  dataIndex: 'gender',
  filters: [
    { text: '男性', value: 'male' },
    { text: '女性', value: 'female' },
  ],
  scopedSlots: { customRender: 'gender' },
  width: '20%',
}, {
  title: '邮件',
  dataIndex: 'email',
  scopedSlots: { customRender: 'email' },
}, {
  title: '操作',
  dataIndex: 'operation',
  scopedSlots: { customRender: 'operation' },
}];

export default {
  data() {
    return {
      data: [],
      pagination: {},
      loading: false,
      columns,
    };
  },
  mounted() {
    this.fetch();
  },
  methods: {
    handleChange (value, key, column) {
      const newData = [...this.data]
      const target = newData.filter(item => key === item.login.uuid)[0]
      if (target) {
        target.editable = true;
        if(column === 'name'){
          const [first, last] = value.split(' ');
          target.name.first = first || '';
          target.name.last = last || '';
          this.data = newData;
          return;
        } else if(column === 'gender') {
          if (['F','female','女', 'f'].includes(value)){
            target[column] = 'female';
          }else {
            target[column] = 'male';
          }
          this.data = newData;
          return;
        }
        target[column] = value;
        this.data = newData;
      }
    },
    edit (key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.login.uuid)[0];
      if (target) {
        target.editable = true;
        this.data = newData;
      }
    },
    save (key) {
      const newData = [...this.data]
      const target = newData.filter(item => key === item.login.uuid)[0]
      if (target) {
        delete target.editable;
        this.data = newData;
        this.cacheData = newData.map(item => ({ ...item }));
      }
    },
    cancel (key) {
      const newData = [...this.data];
      const target = newData.filter(item => key === item.login.uuid)[0];
      if (target) {
        Object.assign(target, this.cacheData.filter(item => key === item.login.uuid)[0]);
        delete target.editable;
        this.data = newData;
      }
    },
    handleTableChange(pagination, filters, sorter) {
      console.log(pagination); // eslint-disable-line
      const pager = { ...this.pagination };
      pager.current = pagination.current;
      this.pagination = pager;
      this.fetch({
        results: pagination.pageSize,
        page: pagination.current,
        sortField: sorter.field,
        sortOrder: sorter.order,
        ...filters,
      });
    },
    fetch(params = {}) {
      console.log('params:', params); // eslint-disable-line
      this.loading = true
      this.axios({
        url: 'https://randomuser.me/api',
        method: 'get',
        params: {
          results: 10,
          ...params,
        },
        responseType: 'json',
      }).then(({data}) => {
        const pagination = { ...this.pagination };
        // Read total count from server
        // pagination.total = data.totalCount;
        pagination.total = 200;
        this.loading = false;
        this.data = data.results;
        this.pagination = pagination;
        this.cacheData = data.results.map(item => ({ ...item }));
      });
    }
  }
};

</script>

<style>
.user-list {
  background: #fff;
}
.user-list .ant-table-pagination.ant-pagination {
  margin-right: 20px;
}
.editable-row-operations a {
  margin-right: 8px;
}
</style>