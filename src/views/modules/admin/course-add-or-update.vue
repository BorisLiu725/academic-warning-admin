<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="课程名" prop="name">
      <el-input v-model="dataForm.name" placeholder="课程名"></el-input>
    </el-form-item>
    <el-form-item label="总学分" prop="totalCredits">
      <el-input v-model="dataForm.totalCredits" placeholder="总学分"></el-input>
    </el-form-item>
    <el-form-item label="课程性质 1-必修 2-选修" prop="nature">
      <el-input v-model="dataForm.nature" placeholder="课程性质 1-必修 2-选修"></el-input>
    </el-form-item>
    <el-form-item label="课程类型 1-线下 2-线上" prop="type">
      <el-input v-model="dataForm.type" placeholder="课程类型 1-线下 2-线上"></el-input>
    </el-form-item>
    <el-form-item label="课程总分" prop="score">
      <el-input v-model="dataForm.score" placeholder="课程总分"></el-input>
    </el-form-item>
    <el-form-item label="达标分数" prop="achievementScore">
      <el-input v-model="dataForm.achievementScore" placeholder="达标分数"></el-input>
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
          name: '',
          totalCredits: '',
          nature: '',
          type: '',
          score: '',
          achievementScore: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          name: [
            { required: true, message: '课程名不能为空', trigger: 'blur' }
          ],
          totalCredits: [
            { required: true, message: '总学分不能为空', trigger: 'blur' }
          ],
          nature: [
            { required: true, message: '课程性质 1-必修 2-选修不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '课程类型 1-线下 2-线上不能为空', trigger: 'blur' }
          ],
          score: [
            { required: true, message: '课程总分不能为空', trigger: 'blur' }
          ],
          achievementScore: [
            { required: true, message: '达标分数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/course/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.course.name
                this.dataForm.totalCredits = data.course.totalCredits
                this.dataForm.nature = data.course.nature
                this.dataForm.type = data.course.type
                this.dataForm.score = data.course.score
                this.dataForm.achievementScore = data.course.achievementScore
                this.dataForm.createTime = data.course.createTime
                this.dataForm.updateTime = data.course.updateTime
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
              url: this.$http.adornUrl(`/admin/course/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'totalCredits': this.dataForm.totalCredits,
                'nature': this.dataForm.nature,
                'type': this.dataForm.type,
                'score': this.dataForm.score,
                'achievementScore': this.dataForm.achievementScore,
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
