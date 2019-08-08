<template lang="html">
  <div class="">
    <!-- 热歌榜 -->
    <MusicList :url="url" />
  </div>
</template>

<script>
  import MusicList from "../../components/Music_List"
  export default {
    data() {
      return {
        url: "/v1/restserver/ting?method=baidu.ting.billboard.billList&type=2&size=5&offset=0"
      }
    },
    components: {
      MusicList
    }
  }
</script>

<style lang="">

</style>