<template>
  <div>
    <div class='table'>
      <el-table border height="600" :data="tableData" highlight-current-row style="width: 100%">
        <el-table-column align='center' width='200' sortable v-for="(item,index) in cols" :key='item.index' :property="item.property"
          :label='item.label'>
          <template slot-scope="scope">
            <el-popover placement="top" width="200" trigger="click" v-model="scope.row[visibleList[index]]">
              <div v-if='item.type == "select"'>
                <el-select class="selectVal" v-model="input" placeholder="请选择">
                  <el-option v-for="item in typeOptions" :key="item.value" :label="item.label" :value="item.value">
                  </el-option>
                </el-select>
              </div>
              <div v-if='item.type == "string"'>
                <el-input class="inputVal" v-model="input" placeholder="请输入内容"></el-input>
              </div>
              <div v-if='item.type == "date"'>
                <el-date-picker style='width:160px;' class="dateVal" v-model="getDate" type="date" value-format="yyyy-MM-dd"
                  placeholder="选择日期" size='mini'>
                </el-date-picker>
              </div>
              <div v-if='item.type == "textarea"'>
                <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="input">
                </el-input>
              </div>
              <div style="text-align: right; margin-top:10px;">
                <el-button size="mini" type="text" @click="noShow()">取消</el-button>
                <el-button type="primary" size="mini" @click="changeData(scope,scope.row)">确定</el-button>
              </div>
              <el-button type="text" slot="reference">{{scope.row[item.property]}}</el-button>
            </el-popover>
          </template>
        </el-table-column>
        <el-table-column align='center' sortable width='120' label='问题场景'>
            <template slot-scope="scope">
              <el-button type="text" size="mini">点击查看</el-button>
            </template>
          </el-table-column>
        <el-table-column align='center' sortable width='120' label='编辑'>
          <template slot-scope="scope">
            <el-button type="danger" size="mini" icon='el-icon-close'>删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
  export default {
    props:{
        tableData:Array,
    },
    data() {
      return {
        cols: [{
            "property": "deviceId",
            "label": '设备编号',
            "type": 'input'
          },
          {
            "property": "device",
            "label": '测试机型',
            "type": 'input'
          },
          {
            "property": "iosOrAndroid",
            "label": '设备类型',
            "type": 'select'
          },
          {
            "property": "ifPass",
            "label": '是否通过',
            "type": 'select'
          },
          {
            "property": "os",
            "label": '系统版本',
            "type": 'input'
          },
          {
            "property": "ram",
            "label": '内存',
            "type": 'select'
          },
          {
            "property": "testItem",
            "label": '测试内容',
            "type": 'textarea'
          },
          {
            "property": "remarks",
            "label": '备注',
            "type": 'textarea'
          },
          {
            "property": "question",
            "label": '问题',
            "type": 'input'
          },
          {
            "property": "testQA",
            "label": '测试QA',
            "type": 'string'
          },
          // {
          //   "property": "pic_url",
          //   "label": '问题场景',
          //   "type": 'string'
          // },
        ],
        visibleList: ['deviceIDVisible', 'deviceVisible', 'iosOrAndroidVisible', 'ifPassVisible'],
        typeOptions: [{
          value: 'IOS',
          label: 'IOS'
        }, {
          value: 'Android',
          label: 'Android'
        }, {
          value: '安卓模拟器',
          label: '安卓模拟器'
        }],
        ifPassOptions: [{
          value: '通过',
          label: '通过'
        }, {
          value: '不通过',
          label: '不通过'
        }],
        ramOptions: [{
          value: '1G',
          label: '1G'
        }, {
          value: '2G',
          label: '2G'
        }, {
          value: '3G',
          label: '3G'
        }, {
          value: '4G及以上',
          label: '4G及以上'
        }],
        value: '男',
        getDate: '',
        input: '',
      }
    },
    methods: {
      changeData(scope, rowDict) {
        var newData = this.input;
        this.input = '';
        for (var key in rowDict) {
          if (rowDict[key] == true) {
            rowDict[key] = false;
            rowDict[key];
            if (key.split('Visible')[0] == 'date') {
              if (this.getDate == '') {
                noShow();
              } else {
                scope.row[key.split('Visible')[0]] = this.getDate;
                this.getDate = ''
              }
            } else {
              if (newData == '') {
                noShow();
              } else {
                scope.row[key.split('Visible')[0]] = newData;
              }

            }
          }
        }
      },
      noShow() {
        $('.el-popover').hide();
      },
      addVisiable() {
        for (var i = 0; i < this.tableData.length; i++) {
          for (var j = 0; j < this.visibleList.length; j++) {
            this.tableData[i][this.visibleList[j]] = false;
          }
        }
      }
    },
    mounted() {
      this.addVisiable();
    },
  }
</script>

<style>
</style>