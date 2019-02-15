
<template>
  <div>
    <ul class="ivu-upload-list">
      <li
        v-for="(item,index) in downList"
        :key="index"
        class="ivu-upload-list-file ivu-upload-list-file-finish"
      >
        <span @click="download(item)">
          <i class="ivu-icon" :class="getFileIcon(item.attType)"></i>
          {{item.attName}}
        </span>
      </li>
    </ul>
  </div>
</template>
<script>
/* 下载附件 */
import config from '@/config'
import { downloadfile } from '@/api/example'
export default {
  name: '',
  data () {
    return {
      downList: []
    }
  },
  props: {
    fileList: Array
  },
  methods: {
    download (item) {
      let options = {
        url: (process.env.NODE_ENV === 'production' ? config.baseUrl.pro : config.baseUrl.dev) + config.downloadFileUrl,
        fileName: item.attName,
        data: { attId: item.attId }
      }
      downloadfile(options).then(res => {

      })
    },
    getFileIcon (attType) {
      if (!attType) return ''
      let type = attType.split('/')[0]
      if (type === 'img' || type === 'IMG') {
        return 'ivu-icon-ios-image'
      } else {
        return 'ivu-icon-ios-stats'
      }
    }
  },
  watch: {
    fileList (list) {
      this.downList = list
    }
  },
  mounted: function () { // 页面初始化执行绑定 在父页面的mounted之前执行

  }
}
</script>
