<template>
  <div class="home">
    <div class="energy-table">
      <el-table
        :data="data"
        :loading="loading"
        width="100%"
        size="small"
        border
        :row-class-name="tableRowClassName"
      >
        <!-- <el-table-column
        label="序号"
        type="index"
      ></el-table-column> -->
        <el-table-column
          align="center"
          prop="recordDate"
          label="日期"
        ></el-table-column>
        <el-table-column
          align="center"
          prop="currentEnergy"
          label="能量"
        ></el-table-column>
        <el-table-column
          align="center"
          prop="todayRevenue"
          label="营收"
        ></el-table-column>
        <!-- <el-table-column
        prop="totalRevenue"
        label="营收累计"
      ></el-table-column> -->
        <el-table-column
          align="center"
          prop="average"
          label="平均"
        ></el-table-column>
        <!-- <el-table-column
          align="center"
          label="操作"
        >
          <template slot-scope="scope">
            <el-button
              type="text"
              size="small"
              @click="deleteData(scope.row)"
            >删除</el-button>
          </template>
        </el-table-column> -->
      </el-table>
    </div>
    <div class="energy-form">
      <el-form
        :model="form"
        :rules="rules"
        inline
        size="small"
        ref="form"
      >
        <el-form-item
          label="能量"
          prop="energy"
        >
          <el-input-number
            v-model="form.energy"
            clearable
            :controls="false"
            placeholder="请输入当前能量"
          ></el-input-number>
        </el-form-item>
        <el-form-item>
          <el-button
            type="primary"
            @click="submit"
          >提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'home',
  // eslint-disable-next-line space-before-function-paren
  data() {
    return {
      data: [],
      total: 0,
      loading: false,
      oriData: {},
      form: {
        energy: undefined
      },
      rules: {
        energy: [
          { required: true, message: '请输入当前能量', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // eslint-disable-next-line space-before-function-paren
    submit() {
      this.$refs['form'].validate(valid => {
        if (valid) {
          this.axios.post('energy/create', this.form)
            .then(response => {
              // console.log(response)
              if (response.data.success) {
                this.getList()
              } else {
                this.$message({
                  type: 'error',
                  message: response.data.message
                })
              }
            })
            .catch(error => {
              this.$message({
                type: 'error',
                message: error
              })
            })
        }
      })
    },
    tableRowClassName ({ row, rowIndex }) {
      if (rowIndex % 2 === 0) {
        return ''
      } else if (rowIndex % 4 === 1) {
        return 'success-row'
      } else {
        return 'warning-row'
      }
    },
    deleteData (row) {
      this.axios.post('energy/delete', { id: row.id })
        .then(response => {
          if (response.data.success) {
            this.getList()
          } else {
            this.$message({
              type: 'error',
              message: response.data.message
            })
          }
        })
        .catch(error => {
          this.$message({
            type: 'error',
            message: error
          })
        })
    },
    // eslint-disable-next-line space-before-function-paren
    getOne() {
      this.axios.get('energy/one')
        .then(response => {
          if (response.data.success) {
            this.oriData = response.data.data[0]
          } else {
            this.$message({
              type: 'error',
              message: response.data.message
            })
          }
        })
        .catch(error => {
          this.$message({
            type: 'error',
            message: error
          })
        })
    },
    // eslint-disable-next-line space-before-function-paren
    getList() {
      this.loading = true
      this.axios.get('energy/list')
        .then(response => {
          if (response.data.success) {
            this.data = response.data.data
          } else {
            this.$message({
              type: 'error',
              message: response.data.message
            })
          }
          this.loading = false
        })
        .catch(error => {
          this.$message({
            type: 'error',
            message: error
          })
          this.loading = false
        })
    }
  },
  mounted () {
    this.getList()
  }
}
</script>
<style lang="less">
.energy-table {
  height: calc(100vh - 70px);
  overflow-x: hidden;
  overflow-y: auto;
}
.energy-form {
  position: fixed;
  left: 0;
  bottom: 0;
  padding-top: 10px;
  width: 100%;
}
.el-table .warning-row {
  background: oldlace;
}

.el-table .success-row {
  background: #f0f9eb;
}
</style>
