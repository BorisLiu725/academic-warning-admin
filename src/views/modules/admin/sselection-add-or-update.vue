<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学号" prop="studentId">
      <el-input v-model="dataForm.studentId" placeholder="学号"></el-input>
    </el-form-item>
    <el-form-item label="选课id" prop="tSelectionId">
      <el-input v-model="dataForm.tSelectionId" placeholder="选课id"></el-input>
    </el-form-item>
    <el-form-item label="终考成绩" prop="finalResults">
      <el-input v-model="dataForm.finalResults" placeholder="终考成绩"></el-input>
    </el-form-item>
    <el-form-item label="作业成绩" prop="taskResults">
      <el-input v-model="dataForm.taskResults" placeholder="作业成绩"></el-input>
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
          studentId: '',
          tSelectionId: '',
          finalResults: '',
          taskResults: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          studentId: [
            { required: true, message: '学号不能为空', trigger: 'blur' }
          ],
          tSelectionId: [
            { required: true, message: '选课id不能为空', trigger: 'blur' }
          ],
          finalResults: [
            { required: true, message: '终考成绩不能为空', trigger: 'blur' }
          ],
          taskResults: [
            { required: true, message: '作业成绩不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/sselection/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.studentId = data.sSelection.studentId
                this.dataForm.tSelectionId = data.sSelection.tSelectionId
                this.dataForm.finalResults = data.sSelection.finalResults
                this.dataForm.taskResults = data.sSelection.taskResults
                this.dataForm.createTime = data.sSelection.createTime
                this.dataForm.updateTime = data.sSelection.updateTime
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
              url: this.$http.adornUrl(`/admin/sselection/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'studentId': this.dataForm.studentId,
                'tSelectionId': this.dataForm.tSelectionId,
                'finalResults': this.dataForm.finalResults,
                'taskResults': this.dataForm.taskResults,
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
