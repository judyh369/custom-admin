<template>
  <div>
    <Upload
      multiple
      :headers="tokenHeaders"
      :action="actionUrl"
      :show-upload-list="false"
      :default-file-list="defaultFileList"
      :format="formats"
      :max-size="fileMaxSize*1024"
      :on-success="handleSuccess"
      :on-format-error="handleFormatError"
      :on-error="handelError"
      :on-exceeded-size="handleMaxSize"
      :before-upload="handleBeforeUpload"
    >
      <Button icon="ios-cloud-upload-outline" type="primary">上传文件</Button>
      <span
        class="ivu-upload-remark"
      >文件大小不能超过:{{fileMaxSize}}；文件个数不能超过:{{fileLength}}个；文件格式：{{fileExtentionNames}}</span>
    </Upload>
    <div>
      <ul class="ivu-upload-list">
        <li
          v-for="(item,index) in uploadList"
          :key="item.attId"
          class="ivu-upload-list-file ivu-upload-list-file-finish"
        >
          <span>
            <i class="ivu-icon" :class="getFileIcon(item.attType)"></i>
            {{item.attName}}
          </span>
          <i class="ivu-icon ivu-icon-ios-close ivu-upload-list-remove" @click="remove(item,index)"></i>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import config from '@/config'
import { getToken } from '@/utils/auth'
import { removeFile } from '@/api/example'
export default {
  data () {
    return {
      tokenHeaders: { Authorization: getToken() },
      actionUrl: (process.env.NODE_ENV === 'production' ? config.baseUrl.pro : config.baseUrl.dev) + config.uploadFileUrl,
      fileMaxSize: 2,
      uploadList: [],
      formats: ['jpg', 'jpeg', 'png', 'zip', 'txt', 'xls', 'pdf'],
      fileLength: 5
    }
  },
  props: {
    action: String,
    defaultFileList: Array,
    sizeLimit: Number
  },
  methods: {
    handleSuccess (res, file) {
      if (res.code === 0) {
        let uploadFile = res.data
        uploadFile.uid = file.uid
        this.uploadList.push(uploadFile)
        this.$Message.success('上传成功')
      } else {
        this.$Message.error('上传失败')
      }
    },
    handleFormatError (file) {
      this.$Notice.warning({
        title: '文件格式错误',
        desc: '文件： ' + file.name + ' 的格式不正确, 正确的格式为：' + this.fileExtentionNames
      })
    },
    handelError (error, file) {
      this.$Notice.warning({
        title: '上传出错',
        desc: '文件  ' + file.name + ' 上传出错：' + error
      })
    },
    remove (item, index) {
      let _this = this
      _this.$Modal.confirm({
        title: '确认删除？',
        onOk: () => {
          removeFile(item.attId).then(res => {
            _this.$Message.success('删除成功')
            _this.uploadList.splice(index, 1)
          })
        }
      })
    },
    handleMaxSize (file) {
      this.$Notice.warning({
        title: '超出文件最大值设置',
        desc: '文件  ' + file.name + ' 太大, 不能超过' + this.fileMaxSize + 'M.'
      })
    },
    handleBeforeUpload (file) {
      console.log(file)
      const check = this.uploadList.length < this.fileLength
      if (!check) {
        this.$Notice.warning({
          title: '文件数量不能超出' + this.fileLength + '.'
        })
      }
      return check
    },
    fileIds () {
      let result = this.uploadList.map(el => {
        if (!(el.id)) {
          return el.id
        }
      }).join(',')
      return result
    },
    getFileIcon (attType) {
      if (!attType) return ''
      console.log(attType)
      let type = attType.split('/')[0]
      if (type === 'img' || type === 'IMG') {
        return 'ivu-icon-ios-image'
      } else {
        return 'ivu-icon-ios-stats'
      }
    },
    initFiles () {
      if (this.action) {
        this.actionUrl = this.action
      }
      if (this.sizeLimit) {
        this.fileMaxSize = this.sizeLimit
      }
    }
  },
  computed: {
    fileExtentionNames () {
      return this.formats.join(',')
    }
  },
  watch: {
    defaultFileList (list) {
      this.uploadList = list.map(el => {
        return { attName: el.name, attType: el.type, size: el.size }
      })
    }
  },
  mounted () {
    this.initFiles()
  }
}
</script>
<style>
.ivu-upload-remark {
  padding-left: 10px;
}
</style>
