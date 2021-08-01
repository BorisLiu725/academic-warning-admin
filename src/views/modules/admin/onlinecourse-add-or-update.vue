<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="课程编号" prop="courseId">
      <el-input v-model="dataForm.courseId" placeholder="课程编号"></el-input>
    </el-form-item>
    <el-form-item label="学习时长" prop="learnTime">
      <el-input v-model="dataForm.learnTime" placeholder="学习时长"></el-input>
    </el-form-item>
    <el-form-item label="达标值" prop="standardValue">
      <el-input v-model="dataForm.standardValue" placeholder="达标值"></el-input>
    </el-form-item>
    <el-form-item label="人员类型 1-学生 2-老师" prop="peopleType">
      <el-input v-model="dataForm.peopleType" placeholder="人员类型 1-学生 2-老师"></el-input>
    </el-form-item>
    <el-form-item label="人员id" prop="peopleId">
      <el-input v-model="dataForm.peopleId" placeholder="人员id"></el-input>
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
          courseId: '',
          learnTime: '',
          standardValue: '',
          peopleType: '',
          peopleId: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          courseId: [
            { required: true, message: '课程编号不能为空', trigger: 'blur' }
          ],
          learnTime: [
            { required: true, message: '学习时长不能为空', trigger: 'blur' }
          ],
          standardValue: [
            { required: true, message: '达标值不能为空', trigger: 'blur' }
          ],
          peopleType: [
            { required: true, message: '人员类型 1-学生 2-老师不能为空', trigger: 'blur' }
          ],
          peopleId: [
            { required: true, message: '人员id不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/onlinecourse/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.courseId = data.onlineCourse.courseId
                this.dataForm.learnTime = data.onlineCourse.learnTime
                this.dataForm.standardValue = data.onlineCourse.standardValue
                this.dataForm.peopleType = data.onlineCourse.peopleType
                this.dataForm.peopleId = data.onlineCourse.peopleId
                this.dataForm.createTime = data.onlineCourse.createTime
                this.dataForm.updateTime = data.onlineCourse.updateTime
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
              url: this.$http.adornUrl(`/admin/onlinecourse/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'courseId': this.dataForm.courseId,
                'learnTime': this.dataForm.learnTime,
                'standardValue': this.dataForm.standardValue,
                'peopleType': this.dataForm.peopleType,
                'peopleId': this.dataForm.peopleId,
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
