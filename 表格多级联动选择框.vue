<template>
      <div>
        <el-table
          :data="lineOrdersData"
          ref="multipleTable"
          row-key="id"
          max-height="450"
          border
          stripe
          selection-row
          @selection-change="handleSelectionChange"
        >
          <el-table-column prop="checked" width="35" :key="checkAll">
            <template slot="header">
              <el-checkbox :indeterminate="isIndeterminate" v-model="checkAll" @change="handleCheckAllChange"></el-checkbox>
            </template>
            <template slot-scope="scope">
              <el-checkbox v-model="scope.row.checked" @change="handleCheckAllChanges(scope.row.checked,scope.row)"></el-checkbox>
            </template>
          </el-table-column>
          <el-table-column prop="name" label="线路名称" align="left" :show-overflow-tooltip="true" width="100"></el-table-column>
          <el-table-column prop="vehicleId" label="配送车辆" width="150" align="left" :show-overflow-tooltip="true">
            <template slot-scope="scope">
              <el-select filterable v-model="scope.row.vehicleId" clearable placeholder="配送车辆" style="width: 120px;">
                <el-option v-for="item in vehicleDic" :key="item.vehicleId" :label="item.vehicleNo" :value="item.vehicleId"></el-option>
              </el-select>
            </template>
          </el-table-column>
          <el-table-column prop="orders" width="120" label="配送司机"></el-table-column>
          <el-table-column prop="orders" width="120" label="配送人员"></el-table-column>
          <el-table-column prop="orders" label="订单信息" align="left" class-name="noneStyle">
            <template slot-scope="scope">
              <el-table
                :data="scope.row.orders"
                tooltip-effect="dark"
                style="width: 100%"
                selection-row
                @select="handleSelectionChange"
                @select-all="selectAll"
              >
                <el-table-column prop="checked" width="35" :key="scope.row.checkAlls">
                  <template slot="header">
                    <el-checkbox v-model="scope.row.checkAlls" @change="handleCheckAllChanged(scope.row.checkAlls,scope.row)"></el-checkbox>
                  </template>
                  <template slot-scope="scope">
                    <el-checkbox v-model="scope.row.checkeds" @change="handleCheckAllChangeds(scope.row.checkeds,scope.row)"></el-checkbox>
                  </template>
                </el-table-column>
                <el-table-column label="客户名称" width="120">
                  <template slot-scope="scope">{{ scope.row.customerName }}</template>
                </el-table-column>
                <el-table-column prop="orderNumber" label="订单编号" width="120"></el-table-column>
                <el-table-column prop="proNameSearchKey" label="配送商品" width="120"></el-table-column>
                <el-table-column prop="配送信息" label="地址">
                  <template slot-scope="scope">
                    {{ scope.row.receivePerson }} . {{scope.row.receivePhone}}
                    <p>{{scope.row.receiveAddress}}</p>
                  </template>
                </el-table-column>
              </el-table>
            </template>
          </el-table-column>
          <
        </el-table>
</template>
<script>
import dayjs from 'dayjs'
export default {
  data() {
    return {
      modalShow: false,
      checkAll: false,
      checkAlls: false,
      checkLen: 0,
      checkLens: 0,
      isIndeterminate: false,
      skuIdList: [],
      skuVisble: false,
      skuNameKey: '',
      checkList: [],
      endTypeList: [],
      skuList: [],
      lineOrdersData: [],
      vehicleDic: [],
      otherData: [],
      selectRows: [],
    }
  },
  computed: {},
  async mounted() {
  },
  created() {},
  methods: {
    async LineOrders() {
        this.lineOrdersData.forEach(item => {
          item.checked = false
          item.checkAlls = false
          if (item.orders.length > 0) {
            item.orders.forEach(obj => {
              obj.fid = item.id
              obj.checkeds = false
            })
          }
        })
    },
    async skuChange() {
      this.LineOrders()
    },
    handleCheckAllChange(val) {
      const newData = this.lineOrdersData.map(item => {
        item.orders.forEach(obj => {
          obj.checkeds = val
        })
        return {
          ...item,
          checked: val,
          checkAlls: val
        }
      })
      this.lineOrdersData = newData
      if (val) {
        this.checkAll = true
        this.checkAlls = true
        this.checkLen = newData.length
      } else {
        this.checkAll = false
        this.checkAlls = false
        this.checkLen = 0
      }
    },
    handleCheckAllChanges(val, row) {
      this.checkLen = val ? this.checkLen + 1 : this.checkLen - 1
      this.checkAll = this.checkLen == this.lineOrdersData.length
      this.lineOrdersData.forEach(item => {
        if (item.id == row.id) {
          item.checkAlls = val
          item.orders.forEach(obj => {
            obj.checkeds = val
          })
        }
      })
      this.$forceUpdate()
    },
    handleCheckAllChanged(val, row) {
      console.log(val, row)
      this.lineOrdersData.forEach(item => {
        if (item.id == row.id) {
          item.checkAlls = val
          item.checked = val
          this.checkLen = val ? this.checkLen + 1 : this.checkLen - 1
          this.checkAll = this.checkLen == this.lineOrdersData.length
          item.orders.forEach(obj => {
            obj.checkeds = val
          })
        }
      })
    },
    handleCheckAllChangeds(val, row) {
      console.log(val, row)
      let lengs = 0
      let leng = 0
      this.lineOrdersData.forEach(item => {
        if (item.id == row.fid) {
          item.orders.forEach(obj => {
            if (obj.checkeds == true) {
              lengs += 1
            }
          })
          this.checkLens = val ? this.checkLens + 1 : this.checkLens - 1
          item.checkAlls = lengs == item.orders.length
          item.checked = lengs > 0
          this.$forceUpdate()
        }
        if (item.checked == true) {
          leng += 1
        }
        this.checkAll = leng == this.lineOrdersData.length
      })
    },
    handleSelectionChange(selection, row) {
      this.selectRows = selection
    },
    selectAll(selection) {
      console.log(selection, 13212313)
    },
  
  }
}
</script>
<style lang="scss" scoped>
.widths {
  width: 70%;
}
</style>
