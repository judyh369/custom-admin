<template>
  <div>
    <Card>
      <Form ref="exampleForm" :model="exampleForm" :label-width="120">
        <FormItem label="姓名">
          <Input v-model="exampleForm.name" placeholder="请输入姓名"></Input>
        </FormItem>
        <FormItem label="地址">
          <Input v-model="exampleForm.address" placeholder="请输入地址"></Input>
        </FormItem>
        <FormItem>
          <file-upload ref="appupload" :defaultFileList="defaultList" :sizeLimit="sizeLimit"></file-upload>
        </FormItem>
        <FormItem class="form-right-group">
          <Button type="primary" @click="handleSubmit">提交</Button>
        </FormItem>
      </Form>
    </Card>
  </div>
</template>
<script>
import { FileUpload } from '@/components/files'
export default {

  data () {
    return {
      fileList: [],
      defaultList: [],
      sizeLimit: 2,
      exampleForm: {
        id: '',
        name: ''
      }
      // actionUrl: '',
    }
  },
  components: { FileUpload },
  methods: {
    handleSubmit () {
      let _this = this
      this.$refs.exampleForm.validate((valid) => {
        _this.$Message.success('提交成功')
      })
    },
    handleReset () {
      this.$refs.exampleForm.resetFields()
    },
    initialFiles () {
      this.defaultList = [{ uid: 1, name: '测试文件1.xls', type: 'application/vnd.ms-excel' },
        { uid: 2, name: '测试文件2.xls', type: 'application/vnd.ms-excel' }]
    }
  },
  mounted () {
    this.initialFiles()
  }

}
</script>
