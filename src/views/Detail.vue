<template>
  <div class="detail">
    <div v-html="html"></div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      html: ""
    };
  },
  created() {
    var url =
      "http://www.dell-lee.com/react/api/detail.json?id=" +
      this.$route.params.id;
    axios.get(url).then(result => {
      if (result.status === 200 && result.data.success) {
        this.html = result.data.data.content;
        console.log(this.html);
      }
    });
  },
  props: {},
  methods: {},
  components: {}
};
</script>

<style lang="scss">
.detail {
  background-color: #fff;
  padding: 20px;
  margin-top: 10px;
}
img {
  float: left;
  margin: 10px 20px 10px 0;
}
</style>