<template>
  <d2-container class="SfcsWo">
    <template slot="header">
      <custom-container-header :isExport="false"
                               :isExportTpl="false"
                               :isImport="false">
        <el-input v-model="formData.PART_NO"
                  :placeholder="$t('wos._1')"
                  clearable
                  style="width:150px"
                  @keyup.enter.native="searchClick" />&nbsp;
        <el-input v-model="formData.WO"
                  :placeholder="$t('wos._2')"
                  clearable
                  style="width:150px"
                  @keyup.enter.native="searchClick" />&nbsp;
        <el-select style="width:150px"
                   v-model="formData.MODEL_ID"
                   @clear="clearModel"
                   filterable
                   clearable
                   :placeholder="$t('spf._2')">
          <div class="ModelHeader">
            <el-input v-model="ModelData.MODELName"
                      :placeholder="$t('spf._32')"
                      clearable
                      style="width:100%"
                      @input="ModelSearch"
                      @keyup.enter.native="ModelSearch" />
            <el-button type="primary"
                       icon="el-icon-search"
                       @click.prevent="ModelSearch">{{ $t("spf._4") }}</el-button>
          </div>
          <el-option v-for="item in ModelList"
                     :key="item.ID"
                     :value="item.ID"
                     :label="item.MODEL" />
          <el-pagination class="ModelDataClass"
                         small
                         :current-page="ModelData.Page"
                         :page-size="ModelData.Limit"
                         :page-sizes="[15, 25, 35, 45]"
                         layout="total, sizes, prev, pager, next, jumper"
                         :total="Modeltotal"
                         @size-change="ModelSizeChange"
                         @current-change="ModelCurrentChange" />
        </el-select>&nbsp;
        <el-select style="width: 150px"
                   clearable
                   v-model="formData.BU_CODE"
                   :placeholder="$t('wos._72')"
                   @clear="removeTag">
          <el-option v-for="item in BuCodeList"
                     :key="item.LOOKUP_CODE"
                     :label="item.MEANING"
                     :value="item.LOOKUP_CODE" />
        </el-select>&nbsp;&nbsp;&nbsp;
        <el-date-picker style="width: 350px"
                        v-model="without"
                        type="datetimerange"
                        range-separator="-"
                        value-format="yyyy-MM-dd"
                        :start-placeholder="$t('wos._19')"
                        :end-placeholder="$t('wos._24')"></el-date-picker>
        <el-button style="margin-left: 9px;"
                   type="primary"
                   icon="el-icon-search"
                   @click.prevent="searchClick">{{ $t("wos._4") }}</el-button>
        <el-button type="danger"
                   icon="el-icon-delete"
                   @click.prevent="eliminateClick">{{ $t("wos._5") }}</el-button>
        <el-button type="success"
                   icon="el-icon-check"
                   v-if="$btnList.indexOf('SfcsWoSave') !== -1"
                   @click.prevent="preserveClick">{{ $t("wos._6") }}</el-button>
      </custom-container-header>
    </template>
    <el-form class="SfcsWo-form"
             ref="form"
             :show-message="false"
             :model="form"
             label-width="100%"
             :rules="rules"
             label-position="top">
      <el-row>
        <el-col :span="8">
          <el-form-item :label="$t('wos._7')"
                        prop="WO_NO">
            <el-input v-model="form.WO_NO"
                      :placeholder="$t('wos._2')"
                      :disabled="wonoEdit"
                      @change="wonoChang" />
          </el-form-item>
          <el-form-item :label="$t('wos._8')">
            <el-input v-model="form.MODEL"
                      disabled />
          </el-form-item>
          <el-form-item :label="$t('wos._9')"
                        prop="ROUTE_ID">
            <el-select style="width:100%"
                       v-model="form.ROUTE_ID"
                       :placeholder="$t('wos._18')"
                       @change="ProcessClick"
                       :disabled="disaEdit">
              <el-option v-for="item in RoutesList"
                         :key="item.ID"
                         :label="item.ROUTE_NAME"
                         :value="item.ID" />
            </el-select>
          </el-form-item>
          <el-form-item :label="$t('wos._57')">
            <el-select :disabled="disaEdit"
                       style="width:100%"
                       v-model="form.TURNIN_TYPE"
                       :placeholder="$t('wos._58')">
              <el-option :label="$t('wos._49')"
                         value="Y" />
              <el-option :label="$t('wos._50')"
                         value="N" />
            </el-select>
          </el-form-item>
          <!-- <el-form-item :label="$t('wos._53')"
                        prop="BU_CODE">
            <el-select style="width:100%"
                       v-model="form.BU_CODE"
                       :placeholder="$t('wos._72')"
                       :disabled="disaEdit">
              <el-option v-for="item in BuCodeList"
                         :key="item.LOOKUP_CODE"
                         :label="item.MEANING"
                         :value="item.LOOKUP_CODE" />
            </el-select>
          </el-form-item> -->
        </el-col>
        <el-col :span="8">
          <!-- <el-form-item :label="$t('wos._47')"
                        prop="STAGE_CODE">
            <el-select style="width:100%"
                       v-model="form.STAGE_CODE"
                       :placeholder="$t('wos._61')"
                       :disabled="disaEdit">
              <el-option v-for="item in ProductStageList"
                         :key="item.LOOKUP_CODE"
                         :label="item.MEANING"
                         :value="item.LOOKUP_CODE" />
            </el-select>
          </el-form-item> -->
          <el-form-item :label="$t('wos._10')"
                        prop="START_DATE">
            <el-date-picker style="width:100%"
                            v-model="form.START_DATE"
                            type="date"
                            :placeholder="$t('wos._19')"
                            :disabled="disaEdit"></el-date-picker>
          </el-form-item>
          <el-form-item :label="$t('wos._11')">
            <el-input type="number"
                      v-model="form.TARGET_QTY"
                      disabled
                      :placeholder="$t('wos._20')" />
          </el-form-item>
          <el-form-item :label="$t('wos._12')">
            <el-input v-model="form.PART_NO"
                      disabled
                      :placeholder="$t('wos._21')" />
          </el-form-item>
          <el-form-item :label="$t('wos._13')">
            <el-input v-model="form.OEM_PN"
                      :placeholder="$t('wos._22')"
                      :disabled="disaEdit" />
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item :label="$t('wos._14')">
            <el-select style="width:100%"
                       v-model="form.PLANT_CODE"
                       :placeholder="$t('wos._23')"
                       disabled>
              <el-option v-for="item in PlantCodeList"
                         :key="item.LOOKUP_CODE"
                         :label="item.MEANING"
                         :value="item.LOOKUP_CODE" />
            </el-select>
          </el-form-item>
          <el-form-item :label="$t('wos._62')">
            <el-select style="width:100%"
                       v-model="form.WO_TYPE"
                       :placeholder="$t('wos._63')"
                       disabled>
              <el-option v-for="item in WoClassificationList"
                         :key="item.LOOKUP_CODE"
                         :label="item.MEANING"
                         :value="item.LOOKUP_CODE" />
            </el-select>
          </el-form-item>
          <el-form-item :label="$t('wos._46')"
                        prop="CLASSIFICATION">
            <el-select style="width:100%"
                       v-model="form.CLASSIFICATION"
                       :placeholder="$t('wos._65')">
              <el-option v-for="item in WoTypeList"
                         :key="item.LOOKUP_CODE"
                         :label="item.MEANING"
                         :value="item.LOOKUP_CODE" />
            </el-select>
          </el-form-item>
          <!-- <el-form-item :label="$t('wos._76')"
                        prop="MANUFACTURE_TYPE">
            <el-select style="width:100%"
                       v-model="form.MANUFACTURE_TYPE"
                       :placeholder="$t('wos._67')"
                       :disabled="disaEdit">
              <el-option v-for="item in ManufactureTypeList"
                         :key="item.LOOKUP_CODE"
                         :label="item.MEANING"
                         :value="item.LOOKUP_CODE" />
            </el-select>
          </el-form-item> -->
          <el-form-item :label="$t('wos._15')"
                        prop="DUE_DATE">
            <el-date-picker style="width:100%"
                            v-model="form.DUE_DATE"
                            type="date"
                            :placeholder="$t('wos._24')"
                            :disabled="disaEdit"></el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-tabs v-model="activeName"
             type="card"
             @tab-click="handleClick"
             style="margin-top:10px">
      <el-tab-pane :label="$t('wos._26')"
                   name="annal">
        <div class="SfcsWo-table">
          <vxe-table v-loading="loading"
                     ref="xTable"
                     border
                     stripe
                     keep-source
                     highlight-hover-row
                     highlight-current-row
                     show-overflow
                     auto-resize
                     width="100%"
                     height="100%"
                     :edit-config="{ mode: 'row', showStatus: true }"
                     :radio-config="{ labelField: 'name', trigger: 'row' }"
                     :data="annalTable"
                     @cell-click="obtain_but">
            <vxe-table-column show-overflow
                              :title="$t('wos._56')"
                              type="radio"
                              min-width="80"
                              align="center" />

            <vxe-table-column show-overflow
                              field="WO_NO"
                              :edit-render="{ name: 'input' }"
                              :title="$t('wos._28')"
                              align="center"
                              min-width="200" />

            <vxe-table-column show-overflow
                              field="PART_NO"
                              :title="$t('wos._12')"
                              :edit-render="{ name: 'input' }"
                              align="center"
                              min-width="150" />
            <vxe-table-column show-overflow
                              field="OEM_PN"
                              :title="$t('wos._13')"
                              :edit-render="{ name: 'input' }"
                              align="center"
                              width="190" />
            <vxe-table-column show-overflow
                              field="MODEL"
                              :title="$t('wos._8')"
                              :edit-render="{ name: 'input' }"
                              align="center"
                              min-width="150" />
            <vxe-table-column show-overflow
                              field="ROUTE_ID"
                              :title="$t('wos._9')"
                              :edit-render="{ name: 'select', options: RoutesListBox }"
                              align="center"
                              min-width="150" />
            <vxe-table-column show-overflow
                              field="TARGET_QTY"
                              :title="$t('wos._33')"
                              :edit-render="{ name: 'input' }"
                              align="center"
                              min-width="160" />
            <vxe-table-column show-overflow
                              field="INPUT_QTY"
                              :title="$t('wos._34')"
                              :edit-render="{ name: 'input' }"
                              align="center"
                              min-width="150" />
            <vxe-table-column min-width="150"
                              show-overflow
                              field="OUTPUT_QTY"
                              :title="$t('wos._35')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="160"
                              show-overflow
                              field="WO_STATUS"
                              :title="$t('wos._36')"
                              :edit-render="{ name: 'select', options: WoStatusListBox }"
                              align="center" />
            <vxe-table-column min-width="220"
                              show-overflow
                              field="START_DATE"
                              :title="$t('wos._10')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="210"
                              show-overflow
                              field="DUE_DATE"
                              :title="$t('wos._15')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="210"
                              show-overflow
                              field="ACTUAL_START_DATE"
                              :title="$t('wos._39')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="200"
                              show-overflow
                              field="ACTUAL_DUE_DATE"
                              :title="$t('wos._40')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="150"
                              show-overflow
                              field="SCRAP_QTY"
                              :title="$t('wos._42')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="150"
                              show-overflow
                              field="PLANT_CODE"
                              :title="$t('wos._14')"
                              :edit-render="{ name: 'select', options: PlantCodeListBox }"
                              align="center" />
            <vxe-table-column min-width="150"
                              show-overflow
                              field="SHIPPED_QTY"
                              :title="$t('wos._44')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="150"
                              show-overflow
                              field="SHIP_DATE"
                              :title="$t('wos._45')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column min-width="150"
                              show-overflow
                              field="CLASSIFICATION"
                              :title="$t('wos._46')"
                              :edit-render="{
                name: 'select',
                options: WoClassificationListBox
              }"
                              align="center" />
            <vxe-table-column min-width="180"
                              show-overflow
                              field="WO_TYPE"
                              :title="$t('wos._55')"
                              :edit-render="{ name: 'select', options: WoTypeListBox }"
                              align="center" />
            <vxe-table-column min-width="230"
                              show-overflow
                              field="STAGE_CODE"
                              :title="$t('wos._47')"
                              :edit-render="{ name: 'select', options: ProductStageListBox }"
                              align="center" />
            <vxe-table-column field="TURNIN_TYPE"
                              :title="$t('wos._48')"
                              :edit-render="{ autofocus: '.custom-input', type: 'visible' }"
                              min-width="200"
                              align="center">
              <template v-slot:edit="{ row }">
                <div class="rddselect">
                  <el-switch disabled
                             v-model="row.TURNIN_TYPE"
                             :active-text="$t('wos._49')"
                             :inactive-text="$t('wos._50')"
                             active-color="#13ce66"
                             inactive-color="#cccccc"
                             active-value="Y"
                             inactive-value="N" />
                </div>
              </template>
            </vxe-table-column>
            <vxe-table-column width="170"
                              show-overflow
                              field="MATERIAL_START_DATE"
                              :title="$t('wos._51')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
            <vxe-table-column width="200"
                              show-overflow
                              field="MANUFACTURE_TYPE"
                              :title="$t('wos._52')"
                              :edit-render="{ name: 'select', options: ManufactureTypeListBox }"
                              align="center" />
            <vxe-table-column width="180"
                              show-overflow
                              field="BU_CODE"
                              :title="$t('wos._53')"
                              :edit-render="{ name: 'select', options: BuCodeListBox }"
                              align="center" />
            <vxe-table-column width="180"
                              show-overflow
                              field="MATERIAL_RELEASED_DATE"
                              :title="$t('wos._54')"
                              :edit-render="{ name: 'input' }"
                              align="center" />
          </vxe-table>
        </div>
        <el-pagination class="SfcsWo-pagination"
                       :current-page="formData.Page"
                       :page-size="formData.Limit"
                       :page-sizes="[15, 25, 35, 45]"
                       layout="total, sizes, prev, pager, next, jumper"
                       :total="total"
                       @size-change="handleSizeChange"
                       @current-change="handleCurrentChange" />
      </el-tab-pane>
      <el-tab-pane :label="$t('wos._27')"
                   name="Detail">
        <div class="SfcsWo-table">
          <vxe-table v-loading="Detailloading"
                     ref="DetailxTable"
                     border
                     stripe
                     keep-source
                     highlight-hover-row
                     highlight-current-row
                     show-overflow
                     auto-resize
                     width="100%"
                     height="100%"
                     :edit-config="{ mode: 'row', showStatus: true }"
                     :radio-config="{ labelField: 'name', trigger: 'row' }"
                     :data="DetailTable">
            <vxe-table-column show-overflow
                              field="CURRENT_OPERATION_ID"
                              :edit-render="{
                name: 'select',
                options: CurrentOperationListBox
              }"
                              :title="$t('wos._68')"
                              align="center" />
            <vxe-table-column show-overflow
                              field="REPAIR_OPERATION_ID"
                              :title="$t('wos._69')"
                              :edit-render="{ name: 'select', options: RepairOperationListBox }"
                              align="center" />
            <vxe-table-column show-overflow
                              field="REWORK_OPERATION_ID"
                              :title="$t('wos._70')"
                              :edit-render="{
                name: 'select',
                options: CurrentOperationListBox
              }"
                              align="center" />
          </vxe-table>
        </div>
        <el-pagination class="SfcsWo-pagination"
                       :current-page="route.Page"
                       :page-size="route.Limit"
                       :page-sizes="[15, 25, 35, 45]"
                       layout="total, sizes, prev, pager, next, jumper"
                       :total="Detailtotal"
                       @size-change="DetailSizeChange"
                       @current-change="DetailCurrentChange" />
      </el-tab-pane>
    </el-tabs>
  </d2-container>
</template>
<script>
import helper from '@/api/SfcsWo'
import helperMO from '@/api/SfcsPrintFilesMapping'
import customContainerHeader from '@/components/custom-container-header'
import { Index, LoadRouteConfig } from '@/api/SfcsRoutes'
const API = helper('SfcsWo')
const APIMO = helperMO('SfcsPrintFilesMapping')
export default {
  name: 'SfcsWo',
  components: {
    customContainerHeader
  },
  data () {
    return {
      rules: {
        WO_NO: [
          {
            required: true,
            message: this.$t('wos._1'),
            trigger: 'blur'
          }
        ],
        STAGE_CODE: [
          {
            required: true,
            message: this.$t('wos._61'),
            trigger: 'change'
          }
        ],
        BU_CODE: [
          {
            required: true,
            message: this.$t('wos._72'),
            trigger: 'change'
          }
        ],
        START_DATE: [
          {
            required: true,
            message: this.$t('wos._19'),
            trigger: 'change'
          }
        ],
        CLASSIFICATION: [
          {
            required: true,
            message: this.$t('wos._65'),
            trigger: 'change'
          }
        ],
        MANUFACTURE_TYPE: [
          {
            required: true,
            message: this.$t('wos._67'),
            trigger: 'change'
          }
        ],
        DUE_DATE: [
          {
            required: true,
            message: this.$t('wos._24'),
            trigger: 'change'
          }
        ],
        ROUTE_ID: [
          {
            required: true,
            message: this.$t('wos._18'),
            trigger: 'change'
          }
        ]
      },
      CurrentOperationList: [],
      CurrentOperationListBox: [],
      RepairOperationList: [],
      RepairOperationListBox: [],
      BuCodeList: [],
      EnabledTypeList: [],
      ManufactureTypeList: [],
      OperationsList: [],
      PlantCodeList: [],
      ProductStageList: [],
      RoutesList: [],
      WoClassificationList: [],
      WoStatusList: [],
      WoTypeList: [],
      RoutesListBox: [],
      WoStatusListBox: [],
      PlantCodeListBox: [],
      WoClassificationListBox: [],
      ProductStageListBox: [],
      WoTypeListBox: [],
      ManufactureTypeListBox: [],
      BuCodeListBox: [],
      activeName: 'annal',
      radio: '',
      loading: false,
      annalTable: [],
      Detailloading: false,
      DetailTable: [],
      total: 0,
      without: '',
      formData: {
        WO_NO: '',
        WO: '',
        BU_CODE: null,
        MODEL_ID: null,
        START_DATE: '',
        DUE_DATE: '',
        PART_NO: '',
        Page: 1,
        Limit: 15
      },
      form: {
        ID: 0,
        WO_NO: '', // 工单号
        MODEL: '',
        WO_STATUS: 0,
        MODEL_ID: '', // 机种
        ROUTE_ID: '', // 制程
        TURNIN_TYPE: '', // 是否自动存仓
        BU_CODE: '', // 制造群
        STAGE_CODE: '', // 工单生产阶段
        START_DATE: '', // 计划投产日期
        TARGET_QTY: '', // 目标产量
        PART_NO: '', // 料号
        PLANT_CODE: '', // 厂部
        OEM_PN: '', // OEM料
        WO_TYPE: '', // 工单类型
        CLASSIFICATION: '', // 工单类别
        MANUFACTURE_TYPE: '', // 制造类别
        DUE_DATE: '' // 计划完成日期
      },
      Detailtotal: 0,
      route: {
        ROUTE_ID: '',
        Page: 1,
        Limit: 15
      },
      disaEdit: false,
      wonoEdit: false,
      diwono: false,
      ModelList: [],
      ModelData: {
        MODELName: '',
        Page: 1,
        Limit: 15
      },
      Modeltotal: 0
    }
  },
  created () {
    this.getIndex()
    this.gainIndex()
    this.getLoadData()
    this.getLoadModelData()
  },
  methods: {
    async getLoadModelData () {
      const res = await APIMO.LoadModelData(this.ModelData)
      console.log(res)
      this.ModelList = res.Result
      if (this.ModelList.length === 0) {
        var obj = {
          DESCRIPTION: '',
          ENABLED: '',
          MODEL: ''
        }
        this.ModelList.push(obj)
      }
      this.Modeltotal = res.TotalCount
    },
    ModelSearch () {
      this.ModelData.Page = 1
      this.getLoadModelData()
    },
    // 分页
    async ModelSizeChange (val) {
      this.ModelData.Limit = val
      this.getLoadModelData()
    },
    async ModelCurrentChange (val) {
      this.ModelData.Page = val
      this.getLoadModelData()
    },
    removeTag () {
      this.formData.BU_CODE = null
    },
    clearModel () {
      this.formData.MODEL_ID = null
    },
    async gainIndex () {
      const res = await Index()
      console.log(res)
      this.RepairOperationList = res.Result.RepairOperationList
      this.CurrentOperationList = res.Result.CurrentOperationList
      this.CurrentOperationListBox.push({
        label: '',
        value: '',
        disabled: false
      })
      this.CurrentOperationList.map(item => {
        this.CurrentOperationListBox.push({
          label: item.DESCRIPTION,
          value: Number(item.ID) ? Number(item.ID) : '',
          disabled: false
        })
      })
      this.RepairOperationListBox.push({
        label: '',
        value: '',
        disabled: false
      })
      this.RepairOperationList.map(item => {
        this.RepairOperationListBox.push({
          label: item.OPERATION_NAME,
          value: Number(item.ID) ? Number(item.ID) : '',
          disabled: false
        })
      })
    },
    async getIndex () {
      const res = await API.Index()
      if (res.Result) {
        this.BuCodeList = res.Result.BuCodeList // 制造群下拉数据
        this.EnabledTypeList = res.Result.EnabledTypeList
        this.ManufactureTypeList = res.Result.ManufactureTypeList
        this.OperationsList = res.Result.OperationsList // 工序下拉数据
        this.PlantCodeList = res.Result.PlantCodeList // 厂部下拉数据
        this.ProductStageList = res.Result.ProductStageList
        this.RoutesList = res.Result.RoutesList // 制程设定下拉数据
        this.WoClassificationList = res.Result.WoClassificationList // 工单类别下拉数据
        this.WoStatusList = res.Result.WoStatusList // 工单状态下拉数据
        this.WoTypeList = res.Result.WoTypeList // 工单类型下拉数据
        var obj = {
          label: '',
          value: '',
          disabled: false
        }
        this.RoutesListBox.push(obj)
        this.RoutesList.map(item => {
          this.RoutesListBox.push({
            label: item.ROUTE_NAME,
            value: Number(item.ID) ? Number(item.ID) : '',
            disabled: false
          })
        })
        this.WoStatusListBox.push(obj)
        this.WoStatusList.map(item => {
          this.WoStatusListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
        this.PlantCodeListBox.push(obj)
        this.PlantCodeList.map(item => {
          this.PlantCodeListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
        this.WoTypeListBox.push(obj)
        this.WoTypeList.map(item => {
          this.WoTypeListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
        this.WoClassificationListBox.push(obj)
        this.WoClassificationList.map(item => {
          this.WoClassificationListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
        this.ProductStageListBox.push(obj)
        this.ProductStageList.map(item => {
          this.ProductStageListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
        this.ManufactureTypeListBox.push(obj)
        this.ManufactureTypeList.map(item => {
          this.ManufactureTypeListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
        this.BuCodeListBox.push(obj)
        this.BuCodeList.map(item => {
          this.BuCodeListBox.push({
            label: item.MEANING,
            value: Number(item.LOOKUP_CODE) ? Number(item.LOOKUP_CODE) : '',
            disabled: false
          })
        })
      }
    },
    // 单号查询
    async getLoadData () {
      if (this.formData.WO) {
        this.formData.WO_NO = this.formData.WO
      }
      this.loading = true
      const res = await API.LoadData(this.formData)
      this.loading = false
      if (res.Result) {
        this.annalTable = res.Result
        console.log(this.annalTable, '获取表格')
        this.total = res.TotalCount
        if (this.diwono) {
          this.diwono = false
          if (this.annalTable.length === 0) {
            this.$message({
              showClose: true,
              message: this.$t('wos._75'),
              type: 'warning'
            })
            this.form.WO_NO = ''
          } else {
            setTimeout(() => {
              this.$refs.xTable.setRadioRow(this.annalTable[0])
              var obj = {
                row: []
              }
              console.log(this.annalTable[0])
              obj.row = this.annalTable[0]
              this.obtain_but(obj)
            }, 500)
          }
        }
      }
    },
    // 点击获取表格一行数据
    async obtain_but (e) {
      var data = e.row
      this.radio = this.annalTable.indexOf(e)
      this.form.ID = data.ID
      this.form.WO_NO = data.WO_NO
      this.form.PART_NO = data.PART_NO
      this.form.BU_CODE = data.BU_CODE
      this.form.MODEL = data.MODEL
      this.form.MODEL_ID = data.MODEL_ID
      this.form.ROUTE_ID = data.ROUTE_ID ? data.ROUTE_ID : ''
      this.form.TURNIN_TYPE = data.TURNIN_TYPE ? data.TURNIN_TYPE : 'N'
      this.form.STAGE_CODE = data.STAGE_CODE ? data.STAGE_CODE : ''
      this.form.START_DATE = data.START_DATE
      this.form.TARGET_QTY = data.TARGET_QTY
      this.form.PLANT_CODE = data.PLANT_CODE ? data.PLANT_CODE : ''
      this.form.OEM_PN = data.OEM_PN
      this.form.WO_TYPE = data.WO_TYPE ? data.WO_TYPE : ''
      this.form.CLASSIFICATION = data.CLASSIFICATION ? data.CLASSIFICATION : ''
      this.form.MANUFACTURE_TYPE = data.MANUFACTURE_TYPE
        ? data.MANUFACTURE_TYPE
        : ''
      this.form.DUE_DATE = data.DUE_DATE
      this.form.WO_STATUS = data.WO_STATUS
      if (data.WO_STATUS === 1) {
        this.disaEdit = false
      } else {
        this.disaEdit = true
      }
      this.wonoEdit = true
      console.log(this.form, ' this.form')
      console.log(e, '点击获取表格一行数据')
    },
    handleClick () {
      if (this.form.ROUTE_ID) {
        this.route.ROUTE_ID = this.form.ROUTE_ID
        this.getProcess()
      }
    },
    //  回车获取工单号
    async wonoChang (e) {
      this.diwono = true
      this.formData.WO_NO = e
      this.getLoadData()
    },
    // 搜索
    async searchClick () {
      this.formData.WO_NO = this.formData.WO
      this.formData.Page = 1
      console.log(this.without)
      if (this.without) {
        this.formData.START_DATE = this.without[0]
        this.formData.DUE_DATE = this.without[1]
      } else {
        this.formData.START_DATE = ''
        this.formData.DUE_DATE = ''
      }
      this.getLoadData()
    },
    async ProcessClick (e) {
      this.route.ROUTE_ID = e
      this.getProcess()
    },
    async getProcess () {
      this.Detailloading = true
      const res = await LoadRouteConfig(this.route)
      this.Detailloading = false
      this.DetailTable = res.Result
      this.Detailtotal = res.TotalCount
      console.log(res)
    },
    // 分页
    async DetailSizeChange (val) {
      this.route.Limit = val
      this.getProcess()
    },
    async DetailCurrentChange (val) {
      this.route.Page = val
      this.getProcess()
    },
    // 清空
    async eliminateClick () {
      this.disaEdit = false
      this.wonoEdit = false
      this.$refs.form.resetFields()
      this.$refs.xTable.clearRadioRow()
      this.form = {
        ID: 0,
        WO_NO: '', // 工单号
        MODEL: '',
        WO_STATUS: 0,
        MODEL_ID: '', // 机种
        ROUTE_ID: '', // 制程
        TURNIN_TYPE: '', // 是否自动存仓
        BU_CODE: '', // 制造群
        STAGE_CODE: '', // 工单生产阶段
        START_DATE: '', // 计划投产日期
        TARGET_QTY: '', // 目标产量
        PART_NO: '', // 料号
        PLANT_CODE: '', // 厂部
        OEM_PN: '', // OEM料
        WO_TYPE: '', // 工单类型
        CLASSIFICATION: '', // 工单类别
        MANUFACTURE_TYPE: '', // 制造类别
        DUE_DATE: '' // 计划完成日期
      }
    },
    // 分页
    async handleSizeChange (val) {
      this.formData.WO_NO = this.formData.WO
      this.formData.Limit = val
      this.getLoadData()
    },
    async handleCurrentChange (val) {
      this.formData.WO_NO = this.formData.WO
      this.formData.Page = val
      this.getLoadData()
    },
    //  保存
    async preserveClick () {
      let startTime = Date.parse(this.form.START_DATE)
      let endTime = Date.parse(this.form.DUE_DATE)

      if (startTime > endTime) {
        this.$message({
          showClose: true,
          message: this.$t('计划完成日期不能大于计划投产日期'),
          type: 'warning'
        })
        return
      }
      this.$refs.form.validate((valid, errInfo) => {
        if (valid) {
          this.saveData()
        } else {
          try {
            Object.keys(errInfo).forEach(item => {
              this.$message.error(errInfo[item][0].message)
              throw Error(errInfo[item][0].message)
            })
          } catch (e) { }
        }
      })
    },
    async saveData () {
      this.form.INPUT_QTY = 0
      this.form.OUTPUT_QTY = 0
      await API.SaveData(this.form).then(response => {
        if (response.Result) {
          this.$notify({
            title: this.$t('wos._73'),
            message: this.$t('wos._74'),
            type: 'success',
            duration: 2000
          })
          this.eliminateClick()
          this.getLoadData()
          this.$refs.form.resetFields()
        }
      })
    }
  }
}
</script>
<style lang="scss" scoped>
.SfcsWo {
  .form-title {
    font-size: 14px;
    color: #333;
    font-weight: 600;
  }

  .SfcsWo-table {
    height: calc(100vh - 515px);
  }
}
</style>
<style>
.SfcsWo-form .el-form-item__content {
  margin-right: 15px;
}
.SfcsWo-form .el-form-item {
  margin-bottom: 0px !important;
}

.SfcsWo-form .el-form-item__label {
  padding: 0 !important;
}

.SfcsWo .el-tabs__header {
  margin: 0;
}

.SfcsWo .radio .el-radio__label {
  display: none;
}

.SfcsWo .SfcsWo-pagination {
  /* border: 1px solid #e6ebf5; */
  margin-top: 5px;
}
.ModelHeader {
  display: flex;
  position: sticky;
  top: 0;
  background: #fff;
  left: 0;
  right: 0;
  z-index: 9;
}
.ModelDataClass {
  position: sticky;
  bottom: 0;
  left: 0;
  right: 0;
  background: #fff;
}
</style>
