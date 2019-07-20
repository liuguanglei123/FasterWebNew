<template>
    <el-container>
        <el-main style="padding: 0; margin-left: 10px;width:43%">

            <div style="position: static;">
                <el-table
                    ref="leftMultipleTable"
                    :data="SuiteData.tests"
                    :show-header="false"
                    :cell-style="{paddingTop: '4px', paddingBottom: '4px'}"
                    @cell-mouse-enter="cellMouseEnter"
                    @cell-mouse-leave="cellMouseLeave"
                    style="width: 100%;"
                    @selection-change="handleleftSelectionChange"
                    v-loading="loading"
                >
                    <!--<el-table-column
                        type="selection"
                        width="40"
                    >
                    </el-table-column>-->

                    <el-table-column
                        min-width=100
                        align="center"
                    >
                        <template slot-scope="scope">
                            <div class="block block_post" v-if="scope.row.method.toUpperCase() === 'POST' ">
                                <span class="small_method block_method_post block_method_color">POST</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_get" v-if="scope.row.method.toUpperCase() === 'GET' ">
                                <span class="small_method block_method_get block_method_color">GET</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_put" v-if="scope.row.method.toUpperCase() === 'PUT' ">
                                <span class="small_method block_method_put block_method_color">PUT</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_delete" v-if="scope.row.method.toUpperCase() === 'DELETE' ">
                                <span class="small_method block_method_delete block_method_color">DELETE</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_patch" v-if="scope.row.method.toUpperCase() === 'PATCH' ">
                                <span class="small_method block_method_patch block_method_color">PATCH</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_head" v-if="scope.row.method.toUpperCase() === 'HEAD' ">
                                <span class="small_method block_method_head block_method_color">HEAD</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_options" v-if="scope.row.method.toUpperCase()=== 'OPTIONS' ">
                                <span class="small_method block_method_options block_method_color">OPTIONS</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                        </template>
                    </el-table-column>


                </el-table>
            </div>

        </el-main>

        <div style="width:8%;">
            <el-button type="primary" style="width:100%;margin-top:200px;margin-bottom:10px" @click="addIntoSuite" >增加<i class="el-icon-arrow-right"></i></el-button>
            <el-button type="primary" style="width:100%;margin-left:0px" @click="rmfromSuite">删除<i class="el-icon-arrow-left"></i></el-button>
        </div>

        <el-main style="padding: 0; margin-left: 10px;">
            <div style="position: static;">
                <el-table
                    :data="selectedData"
                    :show-header="false"
                    :cell-style="{paddingTop: '4px', paddingBottom: '4px'}"
                    style="width: 100%;"
                    @selection-change="handlerightSelectionChange"
                    ref="rightMultipleTable"
                >
                    <el-table-column
                        type="selection"
                        width="40"
                    >
                    </el-table-column>

                    <el-table-column
                        min-width=100
                        align="center"
                    >
                        <template slot-scope="scope">
                            <div class="block block_suite" v-if="scope.row.method.toUpperCase() === 'SUITE' ">
                                <span class="small_method block_method_suite block_method_color">SUITE</span>
                                <span class="small_method block_url">{{scope.row.name}}</span>
                            </div>


                            <div class="block block_post" v-if="scope.row.method.toUpperCase() === 'POST' ">
                                <span class="small_method block_method_post block_method_color">POST</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_get" v-if="scope.row.method.toUpperCase() === 'GET' ">
                                <span class="small_method block_method_get block_method_color">GET</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_put" v-if="scope.row.method.toUpperCase() === 'PUT' ">
                                <span class="small_method block_method_put block_method_color">PUT</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_delete" v-if="scope.row.method.toUpperCase() === 'DELETE' ">
                                <span class="small_method block_method_delete block_method_color">DELETE</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_patch" v-if="scope.row.method.toUpperCase() === 'PATCH' ">
                                <span class="small_method block_method_patch block_method_color">PATCH</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_head" v-if="scope.row.method.toUpperCase() === 'HEAD' ">
                                <span class="small_method block_method_head block_method_color">HEAD</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>

                            <div class="block block_options" v-if="scope.row.method.toUpperCase()=== 'OPTIONS' ">
                                <span class="small_method block_method_options block_method_color">OPTIONS</span>
                                <span class="small_method block_url">{{scope.row.url}}</span>
                                <span class="small-block-summary-description">{{scope.row.name}}</span>
                            </div>
                        </template>
                    </el-table-column>

                </el-table>
            </div>

        </el-main>

    </el-container>
</template>

<script>
    import Report from '../../../reports/DebugReport'

    export default {
        components: {
            Report
        },
        computed: {
            /*selectedData:function () {
                return JSON.parse(JSON.stringify(this.testList))
            }*/
        },
        name: "SuiteShowList",
        props: {
            node: {
                require: true
            },
            project: {
                require: true
            },
            checked: Boolean,
            testList: Array,
            maxindex: {
                type: Number
            },
        },
        data() {
            return {
                reportName: '',
                asyncs: false,
                filterText: '',
                loading: false,
                expand: '&#xe65f;',
                dataTree: {},
                dialogTreeVisible: false,
                dialogTableVisible: false,
                summary: {},
                selectAPI: [],
                currentRow: '',
                currentPage: 1,
                SuiteData: {
                    name: '',
                    id:'',
                    maxindex:0,
                    tests: [],
                    empty:true,
                },
                selectedData:JSON.parse(JSON.stringify(this.testList)),
                unrmAPI:[],
                multipleleftSelection:[],
                multiplerightSelection:[],
                newindex:this.maxindex,
            }
        },
        watch: {
            filterText(val) {
                this.$refs.tree.filter(val);
            },
            node() {
                this.getSuiteList();
            },
            checked() {
                if (this.checked) {
                    this.toggleAll();
                } else {
                    this.toggleClear();
                }
            }
        },

        methods: {
            filterNode(value, data) {
                if (!value) return true;
                return data.label.indexOf(value) !== -1;
            },

            runTree() {
                this.dialogTreeVisible = false;
                const relation = this.$refs.tree.getCheckedKeys();
                if (relation.length === 0) {
                    this.$notify.error({
                        title: '提示',
                        message: '请至少选择一个节点',
                        duration: 1500
                    });
                } else {
                    this.$api.runAPITree({
                        "project": this.project,
                        "relation": relation,
                        "config": this.config,
                        "async": this.asyncs,
                        "name": this.reportName
                    }).then(resp => {
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
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                }
            },
/*            getTree() {
                this.$api.getTree(this.$route.params.id, {params: {type: 3}}).then(resp => {
                    this.dataTree = resp.tree;
                    this.dialogTreeVisible = true;
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },*/

            handleleftSelectionChange(val) {
                this.multipleleftSelection = val;
            },

            handlerightSelectionChange(val){
                this.multiplerightSelection = val;
            },

            toggleAll() {
                this.$refs.multipleTable.toggleAllSelection();
            },

            toggleClear() {
                this.$refs.multipleTable.clearSelection();
            },
            // 查询api列表
            getSuiteList() {
                this.$api.suiteList({
                    params: {
                        node: this.node,
                        project: this.project
                    }
                }).then(res => {
                    this.SuiteData = res;
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },


            handleCurrentChange(val) {
                this.$api.getPaginationBypage({
                    params: {
                        page: this.currentPage,
                        node: this.node,
                        project: this.project
                    }
                }).then(res => {
                    this.SuiteData = res;
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            //删除api
            handleDelApi(index) {
                this.$confirm('此操作将永久删除该API，是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning',
                }).then(() => {
                    this.$api.delAPI(index).then(resp => {
                        if (resp.success) {
                            this.getSuiteList();
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

            // 编辑API
            handleRowClick(row) {
                this.$api.getAPISingle(row.id).then(resp => {
                    if (resp.success) {
                        this.$emit('api', resp);
                    } else {
                        this.$message.error(resp.msg)
                    }
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },
            // 运行API
            handleRunAPI(id) {
                this.loading = true;
                this.$api.runAPIByPk(id, {params: {config: this.config}}).then(resp => {
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

            cellMouseEnter(row) {
                this.currentRow = row;
            },

            cellMouseLeave(row) {
                this.currentRow = '';
            },
            addIntoSuite() {
                if(this.SuiteData.name === ''){
                    this.$message.error("请选择一个测试集！")
                    return;
                }
                this.newindex = this.newindex +1;
                var tmpdata = {
                    'method':'suite',
                    'name': this.SuiteData.name,
                    'index': this.newindex,
                    'flag': 'add',
                    'node':this.node,
                    'project':this.project,
                    'id':this.SuiteData.id,
                }
                this.selectedData.push(tmpdata);
                this.$emit('syncSelectedData',this.selectedData);
                this.$refs.rightMultipleTable.clearSelection();

                /*var data;
                for ( data in this.multipleleftSelection ){
                    this.newindex = this.newindex +1;
                    var tmpdata = {
                        'index': this.newindex,
                        'id': this.multipleleftSelection[data].id,
                        'name': this.multipleleftSelection[data].name,
                        'method': this.multipleleftSelection[data].method,
                        'url': this.multipleleftSelection[data].url,
                        'flag': 'add'
                    }
                    this.selectedData.push(tmpdata);
                }*/
            },
            rmfromSuite() {
                var data;
                for(data in this.multiplerightSelection){
                    this.selectedData.splice(this.multiplerightSelection[data].index-1,1);
                    this.resort(this.selectedData)
                }
                this.$refs.rightMultipleTable.clearSelection();
                this.$emit('syncSelectedData',this.selectedData);
            },
            resort(value){
                var data;
                var num = 0;
                for(data in value){
                    num = num+1;
                    value[data].index = num;
                }
            }
        }
    }
</script>

<style scoped>


</style>
