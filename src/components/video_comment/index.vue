<style lang="less">
.avatar {
  border-radius: 16px;
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar"><span class="h-panel-title">视频评论</span></div>
    <div class="h-panel-body">
      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID"></TableItem>
        <TableItem title="用户">
          <template slot-scope="{ data }">
            <p>
              <img :src="data.user.avatar" width="32" height="32" class="avatar" />
            </p>
            <p>{{data.user.nick_name}}</p>
          </template>
        </TableItem>
        <TableItem title="视频">
          <template slot-scope="{ data }">
            <a :href="'/course/' + data.video.course_id + '/video/' + data.video.id + '/' + data.video.slug" target="_blank">{{data.video.title}}</a>
          </template>
        </TableItem>
        <TableItem title="内容">
          <template slot-scope="{ data }">
            <p v-html="data.render_content"></p>
          </template>
        </TableItem>
        <TableItem prop="created_at" title="时间"></TableItem>
        <TableItem title="操作" align="center" :width="80">
          <template slot-scope="{ data }">
            <Poptip content="确认删除？" @confirm="remove(datas, data)">
              <button class="h-btn h-btn-s h-btn-red">删除</button>
            </Poptip>
          </template>
        </TableItem>
      </Table>
      <p></p>
      <Pagination v-if="pagination.total > 0" align="right" v-model="pagination" @change="changePage" />
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      pagination: {
        page: 1,
        size: 20,
        total: 0
      },
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      R.VideoComment.List(this.pagination).then(resp => {
        this.datas = resp.data.data;
        this.pagination.total = resp.data.total;
        this.pagination.page = resp.data.current_page;
        this.pagination.size = resp.data.per_page;
        this.loading = false;
      });
    },
    remove(data, item) {
      R.VideoComment.Delete({ id: item.id }).then(resp => {
        HeyUI.$Message.success('成功');
        this.getData(true);
      });
    },
  }
};
</script>
