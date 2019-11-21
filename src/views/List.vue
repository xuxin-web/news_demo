<template>
  <div :key="$route.params.id">
    <div class="loading content" v-if="!lists.length">loading</div>
    <div v-else class="content">
      <el-card class="content-item" v-for="(item,i) in lists" :key="i" @click.native="goToDetail(item.id)">
          {{item.title}}
          </el-card>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      lists: []
    };
  },
  props: {},
  created() {
    this.getList(this.$route.params.id);
  },
  methods: {
    getList(id) {
      var url = "http://www.dell-lee.com/react/api/list.json?id=" + id;
      axios
        .get(url)
        .then(result => {
          console.log(result);
          if (result.status === 200 && result.data.success) {
            this.lists = result.data.data;
          } else {
            throw new Error("未获取到数据");
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    goToDetail(id){
        this.$router.push({name:"home-detail",params:{id}})
    }
  },
  watch: {
    $route() {
      this.getList(this.$route.params.id);
    }
  }
};
</script>

<style lang="scss" scoped>
.content {
  margin-top: 20px;
  &-item {
    margin-bottom: 10px;
    cursor: pointer;

  }
}
</style>