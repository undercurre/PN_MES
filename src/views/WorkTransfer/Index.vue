<template>
  <customContainer>
    <!-- 上表 -->
    <el-row :gutter="20"
            style="height: 35%">
      <!-- 左上角 -->
      <el-col :span="10">
        <el-card shadow="always">
          <div slot="header"
               class="clearfix">
            <span>{{ $t("SfcsRework._9") }}</span>
          </div>
          <el-form style="height: 180px"
                   ref="rightTopForm"
                   :model="leftTopForm"
                   status-icon
                   :show-message="false"
                   label-width="60px">
            <el-form-item :label="$t('SfcsRework._10') + '：'">
              <el-input v-model="rightTopForm.MODEL"
                        disabled />
            </el-form-item>
            <el-form-item :label="$t('SfcsRework._11') + '：'">
              <el-input v-model="rightTopForm.PART_NO"
                        disabled />
            </el-form-item>
            <el-form-item :label="$t('SfcsRework._12') + '：'">
              <el-input v-model="rightTopForm.WO_NO"
                        disabled />
            </el-form-item>
            <el-form-item :label="$t('SfcsRework._13') + '：'">
              <el-input v-model="rightTopForm.ROUTENAME"
                        disabled />
            </el-form-item>
          </el-form>
        </el-card>
      </el-col>
      <!-- 右上表 -->
      <el-col :span="14">
        <el-card shadow="always">
          <div slot="header"
               class="clearfix">
            <span>{{ $t("SfcsRework._1") }}</span>
          </div>
          <el-form style="height: 180px"
                   ref="leftTopForm"
                   :model="leftTopForm"
                   status-icon
                   :show-message="false"
                   label-width="90px">
            <el-form-item :label="$t('SfcsRework._39') + '：'">
              <el-radio-group v-model="leftTopForm.TRANSFER_TYPE"
                              @change="changeRadio">
                <el-radio :disabled="isShow"
                          :label="0">{{
                  $t("SfcsRework._37")
                }}</el-radio>
                <el-radio :disabled="isShow"
                          :label="1">{{
                  $t("SfcsRework._38")
                }}</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item :label="$t('SfcsRework._5') + '：'">
              <el-input v-model="leftTopForm.NEW_WORKNO"
                        :disabled="isShow"
                        :placeholder="$t('SfcsRework._6')"
                        @input="changNEW_WORKNO"
                        @keyup.enter.native="addNew">
                <el-button
                           slot="append"
                           icon="el-icon-more"
                           @click="openDialogVisible"
                           :disabled="isShow" />
              </el-input>
            </el-form-item>
            <el-form-item :label="leftTopForm.TRANSFER_TYPE?$t('SfcsRework._34'):$t('SfcsRework._32') + '：'">
              <el-input :disabled="isShow"
                        v-model="leftTopForm.Key"
                        @keyup.enter.native="addSN"
                        :placeholder="leftTopForm.TRANSFER_TYPE?$t('SfcsRework._35'):$t('SfcsRework._33')" />
            </el-form-item>
            <el-form-item style="float: right">
              <el-button type="danger"
                         icon="el-icon-delete"
                         @click="cleanClick">{{ $t("publics.btn.clear") }}</el-button>
              <el-button type="success"
                         icon="el-icon-check"
                         @click="saveClick"
                         v-if="$btnList.indexOf('SfcsReworkSave') !== -1">{{ $t("publics.btn.save") }}</el-button>
            </el-form-item>
          </el-form>
        </el-card>
      </el-col>
    </el-row>
    <!-- 下表 -->
    <el-row :gutter="20"
            style="height: 60%;">
      <el-col :span="6">
        <el-card shadow="always">
          <div slot="header">
            <span>{{ $t("SfcsRework._16") }}</span>
          </div>
          <div class="leftBottom">
            <span>{{ $t("SfcsRework._15") + "："
              }}{{ PrestoreData.ROUTENAME }}</span>
            <!-- eslint-disable-next-line vue/require-v-for-key -->
            <div v-show="!isShow"
                 v-for="(item, index) in ProcessList"
                 @click="changeLeftBottom(item.value, index)"
                 class="radioBox">
              <el-radio style="margin-bottom: 20px"
                        v-model="leftBottom.CHOOSEINDEX"
                        :label="item.value"
                        :disabled="disabledIndex <= index">{{ item.label }}</el-radio>
            </div>
            <!-- eslint-disable-next-line vue/require-v-for-key -->
            <div v-show="isShow"
                 v-for="item in ProcessList"
                 class="radioBox">
              <el-radio style="margin-bottom: 20px"
                        v-model="leftBottom.CHOOSEINDEX"
                        :label="item.value"
                        disabled>{{ item.label }}</el-radio>
            </div>
          </div>
        </el-card>
      </el-col>

      <el-col :span="6" style="float:right">
        <el-tabs type="border-card">
          <!-- 明细内容 -->
          <el-tab-pane  :label="$t('SfcsRework._19')"
                       class="rightBottom">
            <el-row :gutter="20" style="padding:15px">
              <el-col :span="14"> <el-input clearable v-model="leftTopForm.SN" placeholder="请输入流水号"/></el-col>
              <el-col :span="6">
                 <el-button type="primary"
                   icon="el-icon-search"
                   @click.prevent="handleSearch">{{ $t("publics.btn.search") }}</el-button>
              </el-col>
            </el-row>
                <vxe-table
                ref="xTableDetails"
                height="80%"
                width="100%"
                @checkbox-change="checCheckboxkMethod2"
                @checkbox-all="checCheckboxkMethod2"
                :data="detailList">
                <vxe-table-column type="seq" width="60"></vxe-table-column>
                <vxe-table-column type="checkbox" width="60"></vxe-table-column>
                <vxe-table-column field="event" width="200" title="流水号">
                  <template slot-scope="scope">
                    {{scope.row}}
                  </template>
                </vxe-table-column>
              </vxe-table>
            <el-pagination
              style="margin-top:6px"
              background
              layout="prev, pager, next"
              :total="SNTotalCount"
              @current-change="handelPageDetails"
            />
          </el-tab-pane>
          <!-- 历史作业记录 -->
          <el-tab-pane :label="$t('SfcsRework._20')"
                       class="rightBottom">

            <div class="list"
                 v-for="(item, index) in historyList"
                 :key="index">
              {{ item }}
            </div>
          </el-tab-pane>
        </el-tabs>
      </el-col>
    </el-row>
    <!-- 编辑 -->
    <el-dialog :close-on-click-modal="false"
               v-dialogDrag
               :title="$t('SfcsRework._21')"
               width="60%"
               :visible.sync="dialogVisible">
      <el-form ref="editForm"
               label-width="80px"
               :model="editForm">
        <el-form-item :label="$t('SfcsRework._22')">
          <el-input clearable
                    v-model="formData.Key"
                    style="width: 200px"
                    @keyup.enter.native="searchClick"
                    :placeholder="$t('SfcsRework._23')" />
          <el-button type="primary"
                     icon="el-icon-search"
                     @click.prevent="searchClick">{{ $t("publics.btn.search") }}</el-button>
        </el-form-item>
      </el-form>
      <div style="height: 500px; margin-bottom: 10px">
        <vxe-table ref="xTable"
                   border
                   resizable
                   height="100%"
                   size="medium"
                   align="center"
                   highlight-hover-row
                   highlight-current-row
                   show-overflow
                   auto-resize
                   keep-source
                   stripe
                   :loading="loading"
                   :data="mainTable"
                   :mouse-config="{ selected: true }"
                   :radio-config="{ labelField: 'name', trigger: 'row' }"
                   :edit-config="{ trigger: 'click', mode: 'row', showStatus: true }"
                   @cell-click="cellClick">
          <vxe-table-column type="radio"
                            width="60" />
          <vxe-table-column min-width="150"
                            field="WO_NO"
                            :title="$t('SfcsRework._24')"
                            :edit-render="{ name: '$input', props: { readonly: true } }" />
          <vxe-table-column min-width="150"
                            field="PART_NO"
                            :title="$t('SfcsRework._25')"
                            :edit-render="{ name: '$input', props: { readonly: true } }" />
          <vxe-table-column min-width="150"
                            field="INPUT_QTY"
                            :title="$t('SfcsRework._26')"
                            :edit-render="{ name: '$input', props: { readonly: true } }" />
          <vxe-table-column min-width="150"
                            field="OUTPUT_QTY"
                            :title="$t('SfcsRework._27')"
                            :edit-render="{ name: '$input', props: { readonly: true } }" />
        </vxe-table>
      </div>
      <el-pagination :current-page="formData.Page"
                     :page-size="formData.Limit"
                     :page-sizes="[15, 25, 35, 45]"
                     layout="total, sizes, prev, pager, next, jumper"
                     :total="totalPage"
                     @size-change="handleSizeChange"
                     @current-change="handleCurrentChange" />
      <span slot="footer"
            class="dialog-footer">
        <el-button @click="dialogVisible = false">{{
          $t("ssc_rd.cancel")
        }}</el-button>
        <el-button type="primary"
                   @click="addNew">{{
          $t("ssc_rd.sure")
        }}</el-button>
      </span>
    </el-dialog>
  </customContainer>
</template>

<script>
import customContainer from '@/components/custom-container'
import { mapGetters } from 'vuex'
import {
  GetNewWorkNoData,
  GetNewReworkDataByNewNo,
  GetSNDataByOldWoOrCartonNo,
  SaveWoTransferData
} from '@/api/SfcsRework'
import {
  print
} from '@/api/MaterialBarcode'
export default {
  name: 'WorkTransfer',
  components: {
    customContainer
  },
  computed: {
    ...mapGetters(['userinfo'])
  },
  data () {
    return {
      SNTotalCount: 0,
      leftTopForm: {
        SN: '',
        TRANSFER_TYPE: 0,
        NEW_WORKNO: '',
        Key: '',
        Page: 1,
        Limit: 10
      },
      rightTopForm: {},
      leftBottom: {},
      submitForm: {
        ISDELRESOURE: true,
        ISDELUID: true
      },
      dialogVisible: false,
      editForm: {},
      loading: false,
      mainTable: [],
      totalPage: 0,
      formData: {
        Page: 1,
        Limit: 15
      },
      detailList: [],
      historyList: [],
      isShow: false,
      ProcessList: [],
      saveForm: { SNLIST: [], SaveRecords: [] },
      disabledIndex: -1,
      chooseIndex: 0,
      oldIndex: 0,
      PrestoreData: {},
      OldBottom: {},
      OldProcessData: [],
      haveNext: false,
      haveChoose: true,
      showLoading: false

    }
  },
  created () { },
  methods: {
    handleSearch () {
      if (this.saveForm.SNLIST && this.saveForm.SNLIST.length > 0) { this.GetSNDataByOldWoOrCartonNo() }
    },
    checCheckboxkMethod2 ({ records }) {
      this.saveForm.SNLIST = records
    },
    handelPageDetails (e) {
      this.leftTopForm.Page = e
      this.GetSNDataByOldWoOrCartonNo()
    },
    // 选择列表
    async getNewWorkNoData () {
      this.loading = true
      const res = await GetNewWorkNoData(this.formData)
      this.loading = false
      this.mainTable = res.Result ? res.Result : []
      this.totalPage = res.TotalCount ? res.TotalCount : 0
    },
    searchClick () {
      this.formData.Page = 1
      this.getNewWorkNoData()
    },
    // 仓库返查
    async getNewReworkDataByNewNo () {
      const res = await GetNewReworkDataByNewNo(this.leftTopForm)
      const data = res.Result
      if (data) {
        this.rightTopForm = data || {}
        this.PrestoreData = data
        const ProcessList = data.CURRENTOPERATIONLIST || []
        this.ProcessList = []
        ProcessList.map((i) => {
          Object.keys(i).map((j) => {
            this.ProcessList.push({
              label: j,
              value: i[j]
            })
          })
        })
        this.disabledIndex = 99999999
        this.chooseIndex = data.CHOOSEINDEX || 0
      }
    },
    // 返工数据查
    async GetSNDataByOldWoOrCartonNo () {
      const res = await GetSNDataByOldWoOrCartonNo(this.leftTopForm)
      const data = res.Result
      if (!data) return
      // 制程返工模式
      if (this.leftTopForm.TRANSFER_TYPE === 0) {
        const loading = this.$loading({
          lock: true,
          text: 'Loading',
          spinner: 'el-icon-loading',
          background: 'rgba(0, 0, 0, 0.7)',
          target: document.querySelector('#bottomBox')
        })
        this.historyList = []
        this.historyList.push(data.OPERATIONHISTORY)
        this.detailList = data.SNLIST || []
        // const ProcessList = data.CURRENTOPERATIONLIST || []
        this.SNTotalCount = data.SNTotalCount
        // this.ProcessList = []
        // ProcessList.map((i) => {
        //   Object.keys(i).map((j) => {
        //     this.ProcessList.push({
        //       label: j,
        //       value: i[j]
        //     })
        //   })
        // })
        this.disabledIndex = parseInt(data.CHOOSEINDEX) || -1
        this.chooseIndex = data.CHOOSEINDEX || 0
        this.isShow = true
        loading.close()
        // this.leftBottom.CHOOSEINDEX =
        //   Object.values(data.CURRENTOPERATIONLIST[data.CHOOSEINDEX])[0] || 0
      } else if (this.leftTopForm.TRANSFER_TYPE === 1) {
        const loading = this.$loading({
          lock: true,
          text: 'Loading',
          spinner: 'el-icon-loading',
          background: 'rgba(0, 0, 0, 0.7)',
          target: document.querySelector('#bottomBox')
        })
        // 仓库模式
        // const OldProcessData = data.CURRENTOPERATIONLIST || []
        // this.OldProcessData = []
        // OldProcessData.map((i) => {
        //   Object.keys(i).map((j) => {
        //     this.OldProcessData.push({
        //       label: j,
        //       value: i[j]
        //     })
        //   })
        // })
        // this.OldBottom.CHOOSEINDEX =
        //   Object.values(data.CURRENTOPERATIONLIST[data.CHOOSEINDEX])[0] || 0
        this.historyList = []
        this.historyList.push(data.OPERATIONHISTORY)
        this.detailList = data.SNLIST || []
        this.isShow = true
        loading.close()
        console.log(this.historyList, this.detailList)
      }
    },
    // 单选返工类型
    changeRadio (row) {
      this.leftTopForm.TRANSFER_TYPE = row
      if (row === 0) {
        this.leftTopForm.NEW_WORKNO = null
      }
      this.$forceUpdate()
    },
    // 制程选中
    changeLeftBottom (row, index) {
      if (index > this.disabledIndex) {
        return false
      }
      // this.leftBottom.CHOOSEINDEX = row
      console.log(this.leftBottom.CHOOSEINDEX)
      this.ProcessList.map((item, index) => {
        if (Object.values(item).indexOf(row) !== -1) {
          this.chooseIndex = index
        }
      })
      console.log('this.chooseIndex:', this.chooseIndex)
      this.$forceUpdate()
    },
    // 旧制程区选中
    changeOldBottom (row, index) {
      this.OldBottom.CHOOSEINDEX = row
      this.OldProcessData.map((item, index) => {
        if (Object.values(item).indexOf(row) !== -1) {
          this.oldIndex = index
        }
      })
      this.haveChoose = true
      this.$forceUpdate()
    },
    cellClick (row) {
      this.leftTopForm.NEW_WORKNO = row.row.WO_NO
    },
    openDialogVisible () {
      if (this.leftTopForm.TRANSFER_TYPE === undefined) {
        return this.$message.error(this.$t('SfcsRework._28'))
      }
      this.getNewWorkNoData()
      this.dialogVisible = true
    },
    addNew () {
      if (this.leftTopForm.TRANSFER_TYPE === undefined) {
        return this.$message.error(this.$t('SfcsRework._28'))
      }
      this.getNewReworkDataByNewNo()
      this.dialogVisible = false
    },
    addSN () {
      if (this.leftTopForm.TRANSFER_TYPE === undefined) {
        return this.$message.error(this.$t('SfcsRework._28'))
      }
      if (!this.leftTopForm.NEW_WORKNO) {
        return this.$message.error(this.$t('SfcsRework._29'))
      }
      if (!this.leftBottom.CHOOSEINDEX) {
        return this.$message.error(this.$t('SfcsRework._30'))
      }
      this.leftTopForm.CHOOSEINDEXVALUE = this.leftBottom.CHOOSEINDEX
      this.leftTopForm.CARTON_NO = this.leftTopForm.Key
      this.GetSNDataByOldWoOrCartonNo()
    },
    nextClick () {
      this.haveNext = true
      // if (this.leftTopForm.SN) {
      //   this.saveForm.SNLIST.push(this.leftTopForm.SN.toString())
      // }
      this.saveForm = {
        USER_NAME: this.userinfo.USER_NAME,
        TRANSFER_TYPE: this.leftTopForm.TRANSFER_TYPE,
        WO_NO: this.leftTopForm.NEW_WORKNO,
        CARTON_NO: this.leftTopForm.Key,
        SNLIST: null
      }
      // if (this.detailList.length !== 1) {
      //   this.detailList.push(this.leftTopForm.NEW_WORKNO)
      // }
      // delete this.leftTopForm.SN
      // // this.leftTopForm = {}
      // this.rightTopForm = {}
      // this.leftBottom = {}
      // this.submitForm = { ISDELRESOURE: true, ISDELUID: true }
      // this.isShow = false
      // // this.ProcessList = []
      // this.disabledIndex = 99999999
      // this.chooseIndex = 0
      // this.oldIndex = 0
      // this.PrestoreData = {}
      // this.OldBottom = {}
      // this.OldProcessData = []
      this.haveChoose = false
    },
    async saveClick () {
      if (this.leftTopForm.TRANSFER_TYPE === undefined) {
        return this.$message.error(this.$t('SfcsRework._28'))
      }
      if (!this.leftBottom.CHOOSEINDEX) {
        return this.$message.error(this.$t('SfcsRework._31'))
      }
      if (this.leftTopForm.TRANSFER_TYPE === 0) {
        if (this.saveForm.SNLIST && this.saveForm.SNLIST <= 0) {
          return this.$message.error('请选择流水号')
        }
        this.saveForm = {
          USER_NAME: this.userinfo.USER_NAME,
          TRANSFER_TYPE: this.leftTopForm.TRANSFER_TYPE,
          WO_NO: this.leftTopForm.NEW_WORKNO,
          CHOOSEINDEX: this.chooseIndex,
          CHOOSEINDEXVALUE: this.leftBottom.CHOOSEINDEX,
          OLD_WO_NO: this.leftTopForm.Key,
          CARTON_NO: '',
          SNLIST: this.saveForm.SNLIST,
          PLANT_CODE: this.rightTopForm.PLANT_CODE | 0
        }
      } else {
        if (this.haveChoose) {
          this.nextClick()
        }
      }
      await SaveWoTransferData(this.saveForm).then((res) => {
        if (res.ErrorInfo.Status === false) {
          this.$notify({
            title: this.$t('cdc._21'),
            message: this.$t('cdc._22'),
            type: 'success',
            duration: 5000
          })
          if (this.leftTopForm.TRANSFER_TYPE === 1) {
            print({
              PrintTaskId: res.Result,
              Printer: this.userinfo.USER_NAME
            })
              .then(res => {
                if (res.data.Code === 1) {
                  if (res.data.Data) {
                    this.$notify({
                      title: this.$t('publics.tips.success'),
                      message: this.$t('MesBatchManager._52'),
                      type: 'success',
                      duration: 2000
                    })
                  } else {
                    this.$notify({
                      title: this.$t('publics.tips.handleFail'),
                      message: res.data.Msg,
                      type: 'error',
                      duration: 2000
                    })
                  }
                } else {
                  this.$notify({
                    title: this.$t('publics.tips.handleFail'),
                    message: res.data.Msg,
                    type: 'error',
                    duration: 2000
                  })
                }
              })
          }
          this.cleanClick()
        }
      })
    },
    cleanClick () {
      this.leftTopForm = {}
      this.rightTopForm = {}
      this.leftBottom = {}
      this.submitForm = { ISDELRESOURE: true, ISDELUID: true }
      this.detailList = []
      this.historyList = []
      this.isShow = false
      this.ProcessList = []
      this.saveForm = { SNLIST: [], SaveRecords: [] }
      this.disabledIndex = -1
      this.chooseIndex = 0
      this.oldIndex = 0
      this.PrestoreData = {}
      this.OldBottom = {}
      this.OldProcessData = []
      this.haveNext = false
      this.haveChoose = false
    },
    handleSizeChange (val) {
      this.formData.Limit = val
      this.getNewWorkNoData()
    },
    handleCurrentChange (val) {
      this.formData.Page = val
      this.getNewWorkNoData()
    },
    handleGetLabel (item) {
      return Object.values(item)[0]
    },
    changNEW_WORKNO (e) {
      console.log(e)
      this.$forceUpdate()
    }
  }
}
</script>
<style lang="scss" scoped>
.leftBottom {
  height: calc(100vh - 530px);
  overflow: auto;
}
.rightBottom {
  height: calc(100vh - 470px);
}
.bgc {
  background-color: rgb(243, 214, 159);
}
.list {
  background-color: #fff;
  border-bottom: 1px solid #dcdfe6;
  height: 40px;
  line-height: 40px;
  padding: 0 5px;
  color: #606266;
}
.radioBox {
  border: 1px solid #dcdfe6;
  height: 40px;
  line-height: 40px;
  padding-left: 10px;
}
</style>
<style >
.el-tabs--border-card > .el-tabs__content {
  padding: 0;
}
</style>
