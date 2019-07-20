<template>
    <el-container>
        <!--<el-header style="padding: 0; ">
            <div style="padding-top: 8px; padding-left: 10px;">
                <el-pagination
                    @current-change="handleCurrentChange"
                    :current-page.sync="currentPage"
                    :page-size="11"
                    v-show="testData.count !== 0 "
                    background
                    layout="total, prev, pager, next, jumper"
                    :total="testData.count"
                >
                </el-pagination>
            </div>
        </el-header>-->
        <el-input
            style="width: 600px"
            placeholder="请输入接口名称"
            v-model="name"
            clearable
        >
            <template slot="prepend">接口信息录入</template>

            <el-button
                slot="append"
                type="success"
                plain
                @click="saveSuite()"
            >Save
            </el-button>
        </el-input>

        <el-container>
            <el-main style="padding: 0; margin-left: 10px;">
                <el-dialog
                    v-if="dialogTableVisible"
                    :visible.sync="dialogTableVisible"
                    width="70%"
                >
                    <new-debug-report :summary="summary"></new-debug-report>
                </el-dialog>

                <el-dialog
                    title="Run Suite"
                    :visible.sync="dialogTreeVisible"
                    width="45%"
                >
                    <div>
                        <div>
                            <el-row :gutter="2">
                                <el-col :span="8">
                                    <el-switch
                                        style="margin-top: 10px"
                                        v-model="asyncs"
                                        active-color="#13ce66"
                                        inactive-color="#ff4949"
                                        active-text="异步执行（暂不支持）"
                                        inactive-text="同步执行">
                                    </el-switch>
                                </el-col>
                                <el-col :span="10">
                                    <el-input
                                        v-show="asyncs"
                                        clearable
                                        placeholder="请输入报告名称"
                                        v-model="reportName"
                                        :disabled="false">
                                    </el-input>

                                </el-col>
                            </el-row>
                        </div>
                        <div style="margin-top: 20px">
                            <el-input
                                placeholder="输入节点名称进行过滤"
                                v-model="filterText"
                                size="medium"
                                clearable
                                prefix-icon="el-icon-search"
                            >
                            </el-input>

                            <el-tree
                                :filter-node-method="filterNode"
                                :data="dataTree"
                                show-checkbox
                                node-key="id"
                                :expand-on-click-node="false"
                                check-on-click-node
                                :check-strictly="true"
                                :highlight-current="true"
                                ref="tree"
                            >
                            <span class="custom-tree-node"
                                  slot-scope="{ node, data }"
                            >
                                <span><i class="iconfont" v-html="expand"></i>&nbsp;&nbsp;{{ node.label }}</span>
                            </span>
                            </el-tree>
                        </div>

                    </div>
                    <span slot="footer" class="dialog-footer">
                    <el-button @click="dialogTreeVisible = false">取 消</el-button>
                    <el-button type="primary" @click="runTree" v-loading.fullscreen.lock="fullscreenLoading">确 定</el-button>
                  </span>
                </el-dialog>

                <div style="position: fixed; bottom: 0; right:0; left: 500px; top: 160px">
                    <el-table
                        height="calc(100%)"
                        :data="testData.tests"
                        :show-header="false"
                        :cell-style="{paddingTop: '4px', paddingBottom: '4px'}"
                        @cell-mouse-enter="cellMouseEnter"
                        @cell-mouse-leave="cellMouseLeave"
                        style="width: 100%;"
                        @selection-change="handleSelectionChange"
                        v-loading="loading"
                    >
                        <el-table-column
                            type="selection"
                            width="40"
                        >
                        </el-table-column>

                        <el-table-column
                            min-width="800"
                            align="center"
                        >
                            <template slot-scope="scope">
                                <div class="block block_post" v-if="scope.row.method.toUpperCase() === 'POST' ">
                                    <span class="block-method block_method_post block_method_color">POST</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                                <div class="block block_get" v-if="scope.row.method.toUpperCase() === 'GET' ">
                                    <span class="block-method block_method_get block_method_color">GET</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                                <div class="block block_put" v-if="scope.row.method.toUpperCase() === 'PUT' ">
                                    <span class="block-method block_method_put block_method_color">PUT</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                                <div class="block block_delete" v-if="scope.row.method.toUpperCase() === 'DELETE' ">
                                    <span class="block-method block_method_delete block_method_color">DELETE</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                                <div class="block block_patch" v-if="scope.row.method.toUpperCase() === 'PATCH' ">
                                    <span class="block-method block_method_patch block_method_color">PATCH</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                                <div class="block block_head" v-if="scope.row.method.toUpperCase() === 'HEAD' ">
                                    <span class="block-method block_method_head block_method_color">HEAD</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                                <div class="block block_options" v-if="scope.row.method.toUpperCase()=== 'OPTIONS' ">
                                    <span class="block-method block_method_options block_method_color">OPTIONS</span>
                                    <span class="block-method block_url">{{scope.row.url}}</span>
                                    <span class="block-summary-description">{{scope.row.name}}</span>
                                </div>

                            </template>
                        </el-table-column>

                        <el-table-column
                            width="140">
                            <template slot-scope="scope">
                                <el-row v-show="currentRow === scope.row">
                                    <el-button
                                        type="info"
                                        icon="el-icon-edit"
                                        circle size="mini"
                                        @click="handleRowClick(scope.row)"
                                    ></el-button>

                                    <el-button
                                        type="primary"
                                        icon="el-icon-caret-right"
                                        circle size="mini"
                                        @click="handleRunAPI(scope.row)"
                                    ></el-button>
                                    <el-button
                                        type="danger"
                                        icon="el-icon-delete"
                                        circle size="mini"
                                        @click="handleDelApi(scope.row)"
                                    >
                                    </el-button>
                                </el-row>
                            </template>
                        </el-table-column>

                    </el-table>
                </div>

            </el-main>
        </el-container>
    </el-container>

</template>

<script>
    import newDebugReport from '../../../reports/newDebugReport'

    export default {

        name: "SuiteList",
        components: {
            newDebugReport
        },

        props: {
            shouldSave: Boolean,
            run: Boolean,
            config: {
                require: true
            },
            back: Boolean,
            project: {
                require: true
            },
            node: {
                require: false
            },
            del: Boolean
        },

        watch: {
            filterText(val) {
                this.$refs.tree.filter(val);
            },
            testData(){
                this.setTestData();
            },
            run() {
                if(this.shouldSave === true){
                    this.$message('发现上次修改后未保存，请保存！');
                    return;
                }
                this.asyncs = false;
                this.reportName = "";
                this.getTree();
            },
            node() {
                this.getTestList();
            },

            back() {
                this.getTestList();
            },

            del() {
                if (this.selectTest.length !== 0) {
                    this.$confirm('此操作将永久删除测试用例集步骤，是否继续?', '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning',
                    }).then(() => {
                        for(var i=this.selectTest.length; i>0; i--){
                            var data = this.selectTest.pop();
                            for( var each in this.testData.tests){
                                if(this.testData.tests[each].id === data.id && this.testData.tests[each].index === data.index){
                                    this.testData.tests.splice(data.index-1,1);
                                }
                            }
                        }
                        this.resort(this.testData.tests)
                    })
                } else {
                    this.$notify.warning({
                        title: '提示',
                        message: '请至少选择一个用例集',
                        duration: 1000
                    })
                }
            }
        },
        data() {
            return {
                reportName: '',
                asyncs: false,
                filterText: '',
                expand: '&#xe65f;',
                dialogTreeVisible: false,
                dataTree: {},
                loading: false,
                dialogTableVisible: false,
                selectTest: [],
                summary: {},
                currentRow: '',
                testData: {
                    name: this.name,
                    id:'',
                    maxindex:0,
                    tests: [],
                    empty:true,
                },
                currentPage: 1,
                name:'',
                fullscreenLoading:false,
            }
        },

        methods: {
            getTree() {
                this.$api.getTree(this.$route.params.id, {params: {type: 3}}).then(resp => {
                    this.dataTree = resp.tree;
                    this.dialogTreeVisible = true;
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            filterNode(value, data) {
                if (!value) return true;
                return data.label.indexOf(value) !== -1;
            },

            runTree() {
                const relation = this.$refs.tree.getCheckedKeys();
                if (relation.length === 0) {
                    this.$notify.error({
                        title: '提示',
                        message: '请至少选择一个节点',
                        duration: 1500
                    });
                } else {
                    this.fullscreenLoading = true;
                    this.$api.runTestSuiteTree({
                        "project": this.project,
                        "relation": relation,
                        "async": this.asyncs,
                        "name": this.reportName,
                        "config":this.config,
                    }).then(resp => {
                        this.fullscreenLoading = false;
                        if (resp.hasOwnProperty("status")) {
                            this.$message.info({
                                message: resp.msg,
                                duration: 1500
                            });
                        } else {
                            this.summary = resp;
                            this.dialogTableVisible = true;
                        }
                    }).catch(resp => {
                        this.fullscreenLoading = false;
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                    this.dialogTreeVisible = false;
                }
            },

            handleRunTest(id) {
                this.loading = true;
                this.$api.runTestByPk(id, {params: {config: this.config, project: this.project}}).then(resp => {
                    this.summary = resp;
                    this.dialogTableVisible = true;
                    this.loading = false;
                }).catch(resp => {
                    this.loading = false;
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },
            handleCurrentChange(val) {
                this.$api.getTestPaginationBypage({
                    params: {
                        page: this.currentPage,
                        project: this.project,
                        node: this.node
                    }
                }).then(resp => {
                    this.testData = resp;
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            handleEditTest(id) {
                this.$api.editTest(id).then(resp => {
                    this.$emit('testStep', resp);
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            handleCopyTest(id) {
                this.$prompt('请输入用例集名称', '提示', {
                    confirmButtonText: '确定',
                    inputPattern: /^[\s\S]*.*[^\s][\s\S]*$/,
                    inputErrorMessage: '用例集不能为空'
                }).then(({value}) => {
                    this.$api.coptTest(id, {
                        'name': value,
                        'relation': this.node,
                        'project': this.project
                    }).then(resp => {
                        if (resp.success) {
                            this.getTestList();
                        } else {
                            this.$message.error(resp.msg);
                        }
                    }).catch(resp => {
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                })
            },

            handleSelectionChange(val) {
                this.selectTest = val;
            },

            handleDelTest(id) {
                this.$confirm('此操作将永久删除该测试用例集，是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning',
                }).then(() => {
                    this.$api.deleteTest(id).then(resp => {
                        if (resp.success) {
                            this.getTestList();
                        } else {
                            this.$message.error(resp.msg)
                        }
                    }).catch(resp => {
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                })
            },
            getTestList() {
                this.$api.suiteList({
                    params: {
                        project: this.project,
                        node: this.node
                    }
                }).then(resp => {
                    this.testData = resp;
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },
            cellMouseEnter(row) {
                this.currentRow = row;
            },

            cellMouseLeave(row) {
                this.currentRow = '';
            },
            setTestData:function(){
                this.name = this.testData.name;
                this.$emit('transferTestData',this.testData)
            },
            refresh(){
                this.getTestList()
            },
            saveSuite(){
                this.$emit("updateShouleSave",false);
                if(this.name === ''){
                    this.$notify.error({
                        title:"name错误",
                        message:"name不能为空",
                        duration:1500
                    });
                    return false
                }
                if(this.testData.empty === true){
                    this.$api.addSuiteList({
                        name:this.name,
                        relation:this.node,
                        project:this.project,
                        tests: this.testData.tests
                    }).then(resp => {
                        this.refresh();
                        this.$message({
                            message: '添加成功',
                            type: 'success'
                        });
                    }).catch(resp => {
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                }
                else{
                    this.$api.updateSuiteList(this.testData.id,{
                        name:this.name,
                        tests: this.testData.tests
                    }).then(resp => {
                        this.refresh();
                        this.$message({
                            message: '更新成功',
                            type: 'success'
                        });
                    }).catch(resp => {
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                }
            },
            handleRowClick(row) {
                if(this.shouldSave === true){
                    this.$message('发现上次修改后未保存，请保存！');
                    return;
                }
                this.$api.getSuiteStep({
                    params: {
                        project: this.project,
                        node: this.node,
                        index: row.srcindex,
                    }
                }).then(resp => {
                    this.$emit('suiteEdit',resp);
                }).catch(resp => {
                    this.$message.error({
                        message:"服务器连接超时，请重试",
                        duration: 1000
                    })
                })
            },
            // 运行API
            handleRunAPI(row) {
                if(this.shouldSave === true){
                    this.$message('发现上次修改后未保存，请保存！');
                    return;
                }
                this.loading = true;
                var relationArray = new Array(1);
                relationArray[0] = this.node;
                this.$api.runsuitesinglestep({
                    "apiId": row.id,
                    "index":row.index,
                    "relation":relationArray,
                    "project":this.project,
                    "config":this.config
                }).then(resp => {
                    this.summary = resp;
                    this.dialogTableVisible = true;
                    this.loading = false;
                }).catch(resp => {
                    this.loading = false;
                })
            },
            handleDelApi(row) {
                if(this.shouldSave === true){
                    this.$message('发现上次修改后未保存，请保存！');
                    return;
                }
                this.$emit("updateShouleSave",true);
                this.$confirm('此操作会永久删除该步骤，是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning',
                }).then(() => {
                    for(var data in this.testData.tests){
                        if ( this.testData.tests[data].id === row.id && this.testData.tests[data].index === row.index ){
                            this.testData.tests.splice(row.index,1);
                        }
                    }
                    this.resort(this.testData.tests)
                })
            },
            resort(value){
                var data;
                var num = 0;
                for(data in value){
                    num = num+1;
                    value[data].index = num;
                }
            }
        },

        mounted: function () {
            if (this.node !== undefined) {
                this.getTestList();
            }
        }
    }
</script>

<style scoped>

</style>

