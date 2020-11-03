<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="行政区编号" prop="regionId">
      <el-input v-model="dataForm.regionId" placeholder="行政区编号"></el-input>
    </el-form-item>
    <el-form-item label="行政区名称" prop="regionName">
      <el-input v-model="dataForm.regionName" placeholder="行政区名称"></el-input>
    </el-form-item>
    <el-form-item label="类型1.省级 2.市级 3.县区 4.镇 5.村" prop="regionType">
      <el-input v-model="dataForm.regionType" placeholder="类型1.省级 2.市级 3.县区 4.镇 5.村"></el-input>
    </el-form-item>
    <el-form-item label="城乡分类标识111主城区 112城乡结合区 121镇中心区 122镇乡结合区 123特殊区域 210乡中心区 220村庄" prop="countyType">
      <el-input v-model="dataForm.countyType" placeholder="城乡分类标识111主城区 112城乡结合区 121镇中心区 122镇乡结合区 123特殊区域 210乡中心区 220村庄"></el-input>
    </el-form-item>
    <el-form-item label="上级id" prop="pid">
      <el-input v-model="dataForm.pid" placeholder="上级id"></el-input>
    </el-form-item>
    <el-form-item label="创建者" prop="creator">
      <el-input v-model="dataForm.creator" placeholder="创建者"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createDate">
      <el-input v-model="dataForm.createDate" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新者" prop="updater">
      <el-input v-model="dataForm.updater" placeholder="更新者"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateDate">
      <el-input v-model="dataForm.updateDate" placeholder="更新时间"></el-input>
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
          regionId: '',
          regionName: '',
          regionType: '',
          countyType: '',
          pid: '',
          creator: '',
          createDate: '',
          updater: '',
          updateDate: ''
        },
        dataRule: {
          regionId: [
            { required: true, message: '行政区编号不能为空', trigger: 'blur' }
          ],
          regionName: [
            { required: true, message: '行政区名称不能为空', trigger: 'blur' }
          ],
          regionType: [
            { required: true, message: '类型1.省级 2.市级 3.县区 4.镇 5.村不能为空', trigger: 'blur' }
          ],
          countyType: [
            { required: true, message: '城乡分类标识111主城区 112城乡结合区 121镇中心区 122镇乡结合区 123特殊区域 210乡中心区 220村庄不能为空', trigger: 'blur' }
          ],
          pid: [
            { required: true, message: '上级id不能为空', trigger: 'blur' }
          ],
          creator: [
            { required: true, message: '创建者不能为空', trigger: 'blur' }
          ],
          createDate: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updater: [
            { required: true, message: '更新者不能为空', trigger: 'blur' }
          ],
          updateDate: [
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
              url: this.$http.adornUrl(`/region/sysregion/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.regionId = data.sysRegion.regionId
                this.dataForm.regionName = data.sysRegion.regionName
                this.dataForm.regionType = data.sysRegion.regionType
                this.dataForm.countyType = data.sysRegion.countyType
                this.dataForm.pid = data.sysRegion.pid
                this.dataForm.creator = data.sysRegion.creator
                this.dataForm.createDate = data.sysRegion.createDate
                this.dataForm.updater = data.sysRegion.updater
                this.dataForm.updateDate = data.sysRegion.updateDate
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
              url: this.$http.adornUrl(`/region/sysregion/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'regionId': this.dataForm.regionId,
                'regionName': this.dataForm.regionName,
                'regionType': this.dataForm.regionType,
                'countyType': this.dataForm.countyType,
                'pid': this.dataForm.pid,
                'creator': this.dataForm.creator,
                'createDate': this.dataForm.createDate,
                'updater': this.dataForm.updater,
                'updateDate': this.dataForm.updateDate
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
