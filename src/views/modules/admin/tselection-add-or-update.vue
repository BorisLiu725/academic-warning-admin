<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="教师id" prop="teacherId">
      <el-input v-model="dataForm.teacherId" placeholder="教师id"></el-input>
    </el-form-item>
    <el-form-item label="课程id" prop="courseId">
      <el-input v-model="dataForm.courseId" placeholder="课程id"></el-input>
    </el-form-item>
    <el-form-item label="总名额" prop="totalQuota">
      <el-input v-model="dataForm.totalQuota" placeholder="总名额"></el-input>
    </el-form-item>
    <el-form-item label="已选名额" prop="selectedPlaces">
      <el-input v-model="dataForm.selectedPlaces" placeholder="已选名额"></el-input>
    </el-form-item>
    <el-form-item label="课程状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="课程状态"></el-input>
    </el-form-item>
    <el-form-item label="考合格人数" prop="qualifiedE">
      <el-input v-model="dataForm.qualifiedE" placeholder="考合格人数"></el-input>
    </el-form-item>
    <el-form-item label="作业合格人数" prop="qualifiedH">
      <el-input v-model="dataForm.qualifiedH" placeholder="作业合格人数"></el-input>
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
          teacherId: '',
          courseId: '',
          totalQuota: '',
          selectedPlaces: '',
          status: '',
          qualifiedE: '',
          qualifiedH: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          teacherId: [
            { required: true, message: '教师id不能为空', trigger: 'blur' }
          ],
          courseId: [
            { required: true, message: '课程id不能为空', trigger: 'blur' }
          ],
          totalQuota: [
            { required: true, message: '总名额不能为空', trigger: 'blur' }
          ],
          selectedPlaces: [
            { required: true, message: '已选名额不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '课程状态不能为空', trigger: 'blur' }
          ],
          qualifiedE: [
            { required: true, message: '考合格人数不能为空', trigger: 'blur' }
          ],
          qualifiedH: [
            { required: true, message: '作业合格人数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/tselection/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.teacherId = data.tSelection.teacherId
                this.dataForm.courseId = data.tSelection.courseId
                this.dataForm.totalQuota = data.tSelection.totalQuota
                this.dataForm.selectedPlaces = data.tSelection.selectedPlaces
                this.dataForm.status = data.tSelection.status
                this.dataForm.qualifiedE = data.tSelection.qualifiedE
                this.dataForm.qualifiedH = data.tSelection.qualifiedH
                this.dataForm.createTime = data.tSelection.createTime
                this.dataForm.updateTime = data.tSelection.updateTime
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
              url: this.$http.adornUrl(`/admin/tselection/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'teacherId': this.dataForm.teacherId,
                'courseId': this.dataForm.courseId,
                'totalQuota': this.dataForm.totalQuota,
                'selectedPlaces': this.dataForm.selectedPlaces,
                'status': this.dataForm.status,
                'qualifiedE': this.dataForm.qualifiedE,
                'qualifiedH': this.dataForm.qualifiedH,
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
