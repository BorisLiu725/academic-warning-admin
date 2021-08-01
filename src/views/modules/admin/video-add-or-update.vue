<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="人员id" prop="peopleId">
      <el-input v-model="dataForm.peopleId" placeholder="人员id"></el-input>
    </el-form-item>
    <el-form-item label="已完成视频数" prop="finishCount">
      <el-input v-model="dataForm.finishCount" placeholder="已完成视频数"></el-input>
    </el-form-item>
    <el-form-item label="达标值" prop="standardCount">
      <el-input v-model="dataForm.standardCount" placeholder="达标值"></el-input>
    </el-form-item>
    <el-form-item label="完成时长" prop="finishTime">
      <el-input v-model="dataForm.finishTime" placeholder="完成时长"></el-input>
    </el-form-item>
    <el-form-item label="达标值" prop="standardTime">
      <el-input v-model="dataForm.standardTime" placeholder="达标值"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          peopleId: '',
          finishCount: '',
          standardCount: '',
          finishTime: '',
          standardTime: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          peopleId: [
            { required: true, message: '人员id不能为空', trigger: 'blur' }
          ],
          finishCount: [
            { required: true, message: '已完成视频数不能为空', trigger: 'blur' }
          ],
          standardCount: [
            { required: true, message: '达标值不能为空', trigger: 'blur' }
          ],
          finishTime: [
            { required: true, message: '完成时长不能为空', trigger: 'blur' }
          ],
          standardTime: [
            { required: true, message: '达标值不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/admin/video/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.peopleId = data.video.peopleId
                this.dataForm.finishCount = data.video.finishCount
                this.dataForm.standardCount = data.video.standardCount
                this.dataForm.finishTime = data.video.finishTime
                this.dataForm.standardTime = data.video.standardTime
                this.dataForm.createTime = data.video.createTime
                this.dataForm.updateTime = data.video.updateTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/admin/video/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'peopleId': this.dataForm.peopleId,
                'finishCount': this.dataForm.finishCount,
                'standardCount': this.dataForm.standardCount,
                'finishTime': this.dataForm.finishTime,
                'standardTime': this.dataForm.standardTime,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
