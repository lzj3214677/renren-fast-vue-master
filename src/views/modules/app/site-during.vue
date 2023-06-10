<template>
  <div class="mod-config">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="场地" prop="sid">
        <el-select  v-model="dataForm.sid" placeholder="场地" style="width: 48%"  @click.native="getSiteList()" >
          <el-option
            v-for="(item,index) of siteList"
            :key="index"
            :label="item.name"
            :value="item.sid"
          >{{item.name}}</el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="会员" prop="uid">
        <el-select  v-model="dataForm.uid" placeholder="会员" style="width: 48%"  @click.native="getUserList()">
          <el-option
            v-for="(item,index) of userList"
            :key="index"
            :label="item.realname"
            :value="item.uid"
          >{{item.realname}}</el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="预定日期" prop="duringDate" style="width: 48%">
        <el-date-picker style="width: 90%"
                        placeholder="预定日期"
                        value-format="yyyy-MM-dd 00:00:00"
                        v-model="dataForm.duringDate"
        ></el-date-picker>
      </el-form-item>

      <el-form-item label="预定时间" prop="duringTime" style="width: 48%">
        <el-select  v-model="dataForm.duringTime" placeholder="预定时间" style="width: 90%" >
          <el-option
            v-for="(item,index) of timeList"
            :key="index"
            :label="item.label"
            :value="item.value"
          >{{item.label}}</el-option>
        </el-select>
      </el-form-item>
    </el-form>

    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </div>
</template>

<script>
export default {
  data () {
    return {
      dataForm: {
        sid: '',
        uid: '',
        duringDate: '',
        duringTime: ''
      },
      siteList: [],
      userList: [],
      timeList: [
        {
          label: '06:00',
          value: '06:00'
        },
        {
          label: '07:00',
          value: '07:00'
        },
        {
          label: '08:00',
          value: '08:00'
        },
        {
          label: '09:00',
          value: '09:00'
        },
        {
          label: '10:00',
          value: '10:00'
        },
        {
          label: '11:00',
          value: '11:00'
        },
        {
          label: '12:00',
          value: '12:00'
        },
        {
          label: '13:00',
          value: '13:00'
        },
        {
          label: '14:00',
          value: '14:00'
        },
        {
          label: '15:00',
          value: '15:00'
        },
        {
          label: '16:00',
          value: '16:00'
        },
        {
          label: '17:00',
          value: '17:00'
        },
        {
          label: '18:00',
          value: '18:00'
        },
        {
          label: '19:00',
          value: '19:00'
        },
        {
          label: '20:00',
          value: '20:00'
        },
        {
          label: '21:00',
          value: '21:00'
        }
      ]
    }
  },
  components: {
  },
  activated () {
  },
  methods: {
    // 获取数据列表
    getSiteList () {
      this.$http({
        url: this.$http.adornUrl('/app/site/list'),
        method: 'get',
      }).then(({data}) => {
        this.siteList = data.page.list
      })
    },
    getUserList () {
      this.$http({
        url: this.$http.adornUrl('/app/user/list'),
        method: 'get',
      }).then(({data}) => {
        this.userList = data.page.list
      })
    },
    dataFormSubmit () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/app/during/save`),
            method: 'post',
            data: this.$http.adornData({
              'uid': this.dataForm.uid,
              'sid': this.dataForm.sid,
              'duringDate': this.dataForm.duringDate,
              'duringTime': this.dataForm.duringTime
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '预定成功',
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
    },
  }
}
</script>
