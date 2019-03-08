<template>
    <div>
        <!-- table -->
        <div>
            <el-table border :data="tableData" style="width: 100%">
                <!-- v-for循环出列表 -->
                <el-table-column align='center' v-for='(item,index) in cols' :key='item.index' :prop="item.prop" :label="item.label"
                    width="250">
                    <template slot-scope="scope">
                        <span>{{scope.row[item.prop]}}</span>
                    </template>
                </el-table-column>
                <!-- 编辑列 -->
                <el-table-column align='center' type='index' resizable sortable width='240' label='编辑'>
                    <template slot-scope="scope">
                        <button class="editButton" size="mini" @click="editRow($event)" icon='el-icon-close'>编辑</button>
                        <button class="quitButton" size="mini" @click="noSave($event,scope.$index)" icon='el-icon-close'>取消</button>
                        <button class="deleteButton" size="mini" @click="deleteRow(scope.$index,tableData)" icon='el-icon-close'>删除</button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
        <!-- 分页 -->
        <div class="block">
            <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage"
                :page-sizes="pagesizes" :page-size="pagesize" layout="total, sizes, prev, pager, next, jumper"
                :total="dataTotal">
            </el-pagination>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                // 列表col相关数据
                cols: [{
                    "prop": "date",
                    "label": "日期",
                    'type': 'date',
                }, {
                    "prop": "name",
                    "label": "姓名",
                    'type': 'string'
                }, {
                    "prop": "sex",
                    "label": "性别",
                    'type': 'select',
                    'option': ['男', '女']
                }],
                // 后台接收列表数据tableData
                tableData: [{
                    id: '12',
                    date: '2016-05-02',
                    name: '王小虎',
                    sex: '男'
                }, {
                    id: '233',
                    date: '2016-05-04',
                    name: '王小虎',
                    sex: '女'
                }, {
                    id: '344',
                    date: '2016-05-01',
                    name: '王小虎',
                    sex: '男'
                }, {
                    id: '43',
                    date: '2016-05-03',
                    name: '王小虎',
                    sex: '女'
                }],
                // 父组件传入
                saveUrl: '',
                delUrl: '/apis/compatibilityTest/deleteDataToMysql/',
                tableName: '20190218_L12全面屏兼容性测试_陈彬彬_L12_compatibilityReport',
                //分页
                currentPage: 4,
                dataTotal:1024,
                pagesizes:[100, 200, 300, 400],
                pagesize:100,
            }
        },
        methods: {
            // 创建下拉
            createSelect(optionList, value) {
                var html = '<select class="selectVal">';
                for (var i = 0; i < optionList.length; i++) {
                    if (value == optionList[i]) {
                        html += `<option value="${optionList[i]}" selected>${optionList[i]}</option>`;
                    } else {
                        html += `<option value="${optionList[i]}">${optionList[i]}</option>`;
                    }
                }
                html += '</select>';
                return html
            },

            // 编辑功能
            editRow(object) {
                var _this = object.target;
                var saveUrl = this.saveUrl;
                //获取当前行目标
                var parentRow = $(object.target).parent().parent().parent();
                if ($(_this).hasClass('editing') == true) {
                    $.post(saveUrl, {
                        Sheet: tableName,
                        id: data[index]['id'],
                        deviceId: value,
                    }, function (result) {
                        result = JSON.parse(result);
                        if (result == 'success') {
                            $(_this).removeClass('editing')
                            $(parentRow).children().each(function (index) {
                                if (index < $(parentRow).children().length - 1) {
                                    var cellVal = $(this).children('.cell').children('span').children()
                                        .val();
                                    $(this).children('.cell').children('span').html(cellVal);
                                } else {
                                    $(this).children('.cell').children('button:first-child').html('编辑');
                                    $(this).children('.cell').children('button:nth-child(2)').hide();
                                }
                            })
                        } else {
                            alert('保存失败');
                        }
                    });
                } else {
                    //遍历行
                    var cols = this.cols;
                    var createSelect = this.createSelect;
                    $(parentRow).children().each(function (index) {
                        var beforeEditVal = $(this).children('.cell').children('span').text();
                        var cellVal = $(this).children('.cell').children('span').html();
                        if (index < $(parentRow).children().length - 1) {
                            if (cols[index].type == 'string') {
                                var html =
                                    `<input type='text' value="${cellVal}" class="editRow" placeholder="请输入内容">`;
                            } else if (cols[index].type == 'textarea') {
                                var html = `<textarea class="editRow" value="${cellVal}"></textarea>`;
                            } else if (cols[index].type == 'date') {
                                var html = `<input type="date" class="editRow" value="${cellVal}">`;
                            } else if (cols[index].type == 'select') {
                                var html = createSelect(cols[index].option, cellVal);
                            }
                            $(this).children('.cell').children('span').html(html);
                            //将按钮内容变成保存
                        } else {
                            $(this).children('.cell').children('button:first-child').html('保存');
                            $(this).children('.cell').children('button:nth-child(2)').show();
                        }
                    })
                    $(_this).addClass('editing');
                    console.log('className', $(_this).prop('className'));
                }
            },

            //取消保存
            noSave(object, index) {
                var _this = object.target;
                var cols = this.cols;
                var tableData = this.tableData;
                console.log('index', tableData[index]);
                var keyList = new Array;
                for (var key in tableData[index]) {
                    for (var i = 0; i < cols.length; i++) {
                        if (cols[i].prop == key) {
                            keyList.push(tableData[index][key]);
                        }
                    }

                }
                console.log('keyList', keyList)
                var parentRow = $(object.target).parent().parent().parent();
                var getId = parentRow.children('td:first-child').children().children().html();
                console.log('当前点击目标', getId);
                $(parentRow).children().each(function (index) {
                    if (index < $(parentRow).children().length - 1) {
                        $(this).children('.cell').children('span').html(keyList[index]);
                    } else {
                        $(this).children('.cell').children('button:first-child').html('编辑');
                        $(this).children('.cell').children('button:first-child').removeClass('editing');
                        $(this).children('.cell').children('button:nth-child(2)').hide();
                    }
                })
            },

            //删除
            deleteRow(index, rows) {
                rows.splice(index, 1);
                var delId = rows[index].id;
                var tableName = this.tableName;
                var delUrl = this.detUrl;
                var _this = this;
                $.post(delUrl, {
                    Sheet: tableName,
                    id: this.deiId,
                    deviceId: value,
                }, function (result) {
                    result = JSON.parse(result);
                    _this.tableData = result;
                });
            },

            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                console.log(`当前页: ${val}`);

            }
        }
    }
</script>
<style>
    .editButton,
    .quitButton {
        border-radius: 5px;
        padding: 2px;
        background: #00c0ef;
        color: white;
        border: 1px solid #00c0ef;
        width: 60px;
        height: 25px;
    }

    .deleteButton {
        border-radius: 5px;
        padding: 2px;
        background: #dd4b39;
        color: white;
        border: 1px solid #dd4b39;
        width: 60px;
        height: 25px;
        margin-left: 10px;
    }

    .deleteButton {
        background: #d73925;
        border: 1px solid #d73925;
    }

    .editButton:hover,
    .quitButton:hover {
        background: #00acd6;
        border: 1px solid #00acd6;
    }

    .quitButton {
        margin-left: 10px;
        display: none;
    }

    .parent {
        width: 20px;
        height: 20px;
        display: inline;
    }

    .block {
        margin-top: 30px;
        margin-right: 100px;
        float: right;
    }
</style>