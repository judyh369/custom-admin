<template>
  <Card>
    <Form ref="exampleForm" :label-width="120">
      <FormItem label="附件：">
        <file-download :fileList="downList"></file-download>
      </FormItem>
    </Form>
  </Card>
</template>
<script>

import { FileDownload } from '@/components/files'
export default {

  data () {
    return {
      downList: []
    }
  },
  components: {
    FileDownload
  },
  methods: {
    initFiles () {
      this.downList = [{ attId: 1, attName: '测试文件1.xls', attType: 'application/vnd.ms-excel' },
        { attId: 2, attName: '测试文件2.xls', attType: 'application/vnd.ms-excel' }]
    }
  },
  mounted () {
    this.initFiles()
  }
}
</script>
