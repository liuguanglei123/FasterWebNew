<template>

    <el-container>
        <el-header style="background: #fff; padding: 0; ">
            <div class="nav-api-header">
                <div style="padding-top: 10px; margin-left: 10px">
                    <el-button
                        type="primary"
                        size="small"
                        icon="el-icon-circle-plus"
                        @click="dialogVisible = true"
                        :disabled="!addTestActivate"
                    >
                        新建步骤集
                    </el-button>

                    <el-dialog
                        title="新建步骤集"
                        :visible.sync="dialogVisible"
                        width="30%"
                        align="center"
                    >
                        <el-form
                            :model="nodeForm"
                            :rules="rules"
                            ref="nodeForm"
                            label-width="100px"
                            class="project">
                            <el-form-item label="节点名称" prop="name">
                                <el-input v-model="nodeForm.name"></el-input>
                            </el-form-item>
                        </el-form>

                        <el-radio-group v-model="radio" size="small">
                            <el-radio-button label="根节点"></el-radio-button>
                            <el-radio-button label="子节点"></el-radio-button>
                        </el-radio-group>

                        <span slot="footer" class="dialog-footer">
                        <el-button @click="dialogVisible = false">取 消</el-button>
                        <el-button type="primary" @click="handleConfirm('nodeForm')">确 定</el-button>
                      </span>
                    </el-dialog>
                    <el-button
                        type="danger"
                        size="small"
                        icon="el-icon-delete"
                        @click="deleteNode"
                        :disabled="buttonActivate"
                    >删除步骤集
                    </el-button>

                    <el-button
                        :disabled="currentNode === '' "
                        type="info"
                        size="small"
                        icon="el-icon-edit-outline"
                        @click="renameNode"
                    >节点重命名
                    </el-button>

                    <el-button
                        type="warning"
                        size="small"
                        icon="el-icon-circle-plus-outline"
                        @click="addstepdlgshow=true"
                        :disabled="buttonActivate"
                    >添加步骤
                    </el-button>
                    <!--新建步骤集对话框-->
                    <el-dialog
                        title="添加步骤"
                        :visible.sync="addstepdlgshow"
                        width="90%"
                        align="center"
                        style="height:auto;"
                        top="10vh"
                    >
                        <!--<el-tabs v-model="activetName" @tab-click="handleClick" type="border-card">-->
                        <el-tabs v-model="activetName" type="border-card">
                            <el-tab-pane label="API" name="API">
                                <el-container>
                                    <el-aside
                                        style="margin-top:10px;width:17%;padding-right:40px"
                                        v-show="addTestActivate"
                                    >
                                        <div class="nav-api-side-dlg">
                                            <div class="api-tree" style="height:450px">
                                                <el-input
                                                    placeholder="输入关键字进行过滤"
                                                    v-model="filterText"
                                                    size="medium"
                                                    clearable
                                                    prefix-icon="el-icon-search"
                                                >
                                                </el-input>

                                                <el-tree
                                                    @node-click="handleAPINodeClick"
                                                    :data="APIdataTree"
                                                    node-key="id"
                                                    :default-expand-all="false"
                                                    :expand-on-click-node="false"
                                                    :draggable="false"
                                                    highlight-current
                                                    :filter-node-method="filterNode"
                                                    ref="tree1"
                                                    @node-drag-end="handleDragEnd"
                                                >
                                            <span class="custom-tree-node"
                                                  slot-scope="{ node, data }"
                                            >
                                                <span><i class="iconfont" v-html="expand"></i>&nbsp;&nbsp;{{ node.label }}</span>
                                            </span>
                                                </el-tree>
                                            </div>
                                        </div>
                                    </el-aside>
                                    <el-main>
                                        <api-show-list
                                            :checked="checked"
                                            v-on:api="handleAPI"
                                            :node="currentAPINode !== '' ? currentAPINode.id : '' "
                                            :project="$route.params.id"
                                            :testList="testListData.tests"
                                            :maxindex="testListData.maxindex"
                                            ref="apiShowList"
                                            v-if="addstepdlgshow"
                                        >
                                        </api-show-list>

                                    </el-main>

                                </el-container>
                            </el-tab-pane>
                        </el-tabs>

                        <span slot="footer" class="dialog-footer">
                        <el-button @click="addstepdlgshow = false">取 消</el-button>
                        <el-button type="primary" @click="saveSuite()">确 定</el-button>
                      </span>
                    </el-dialog>
                    <!--<el-button
                        type="primary"
                        plain
                        size="small"
                        icon="el-icon-upload"
                        :disabled="buttonActivate"
                    >导入用例
                    </el-button>

                    <el-button
                        type="info"
                        plain
                        size="small"
                        icon="el-icon-download"
                        :disabled="buttonActivate"
                    >导出用例
                    </el-button>-->


                    <!--<el-button
                        style="margin-left: 20px"
                        type="danger"
                        icon="el-icon-delete"
                        circle
                        size="mini"
                        :disabled="buttonActivate"
                        @click="del = !del"
                    ></el-button>
                    TODO:删除操作未验证-->


                    <!--<el-tooltip
                        class="item"
                        effect="dark"
                        content="环境信息"
                        placement="top-start"
                    >
                        <el-button plain size="small" icon="el-icon-view"></el-button>
                    </el-tooltip>-->

                    &nbsp配置选择:
                    <el-select
                        placeholder="请选择"
                        size="small"
                        tyle="margin-left: -6px"
                        v-model="currentConfig"
                    >
                        <el-option
                            v-for="item in configOptions"
                            :key="item.id"
                            :label="item.name"
                            :value="item.id">
                        </el-option>
                    </el-select>

                    <el-button
                        style="margin-left: 20px"
                        type="primary"
                        icon="el-icon-caret-right"
                        circle
                        size="mini"
                        @click="run = !run"
                    ></el-button>

                </div>
            </div>
        </el-header>

        <el-container>
            <el-aside
                style="margin-top: 10px;"
            >
                <div class="nav-api-side">
                    <div class="api-tree">
                        <el-input
                            placeholder="输入关键字进行过滤"
                            v-model="filterText"
                            size="medium"
                            clearable
                            prefix-icon="el-icon-search"
                        >
                        </el-input>

                        <el-tree
                            @node-click="handleNodeClick"
                            :data="dataTree"
                            node-key="id"
                            :default-expand-all="false"
                            :expand-on-click-node="false"
                            :draggable="false"
                            highlight-current
                            :filter-node-method="filterNode"
                            ref="tree2"
                            @node-drag-end="handleDragEnd"
                        >
                            <span class="custom-tree-node"
                                  slot-scope="{ node, data }"
                            >
                                <span><i class="iconfont" v-html="expand"></i>&nbsp;&nbsp;{{ node.label }}</span>
                            </span>
                        </el-tree>

                    </div>
                </div>


            </el-aside>

            <el-main style="padding: 0;">
                <suite-body
                    v-if="!suiteEditFlag"
                    :nodeId="currentNode.id"
                    :project="$route.params.id"
                    :response="response"
                    :config="currentConfig"
                >
                </suite-body>
                <suite-list
                    v-if="suiteEditFlag"
                    :project="$route.params.id"
                    :node="currentNode.id"
                    :del="del"
                    :config="currentConfig"
                    v-on:testStep="handleTestStep"
                    :back="back"
                    :run="run"
                    style="width:40%"
                    @transferTestData="getTestData"
                    ref="suiteList"
                    v-on:suiteEdit="handleAPI"
                >
                </suite-list>

            </el-main>
        </el-container>
    </el-container>
</template>

<script>
    import SuiteBody from './components/SuiteBody'
    import SuiteList from './components/SuiteList'
    import ApiShowList from './components/ApiShowList'

    export default {
        watch: {
            filterText(val) {
                this.$refs.tree2.filter(val);
            }
        },
        components: {
            SuiteBody,
            SuiteList,
            ApiShowList,
        },
        data() {
            return {
                testStepResp: [],
                nodeForm: {
                    name: '',
                },
                rules: {
                    name: [
                        {required: true, message: '请输入节点名称', trigger: 'blur'},
                        {min: 1, max: 50, message: '最多不超过50个字符', trigger: 'blur'}
                    ]
                },
                back: false,
                run: false,
                radio: '根节点',
                addTestActivate: true,
                currentConfig: '',
                treeId: '',
                APItreeId: '',
                maxId: '',
                APImaxId: '',
                dialogVisible: false,
                currentNode: '',
                data: '',
                currentAPINode: '',
                APIdata:'',
                filterText: '',
                expand: '&#xe65f;',
                dataTree: [],
                APIdataTree: [],
                configOptions: [{
                    value: '测试环境',
                    label: '测试环境'
                }],
                addstepdlgshow: false,
                activetName: 'API',
                addAPIFlag: false,
                checked: false,
                del: false,
                run: false,
                back: false,
                testListData: {
                    name: '',
                    maxindex:0,
                    tests: [],
                    empty:true,
                },
                response:'',
                suiteEditFlag:true
            }
        },
        computed: {
            buttonActivate: {
                get: function () {
                    return !this.addTestActivate || this.currentNode === '';
                },
                set: function (value) {
                    this.addTestActivate = value;
                    this.testStepResp = [];
                }
            },
            transferApi: {
                get: function () {
                    if(this.currentAPINode === ''){
                        return [];
                    }
                    else{
                        const result=[];
                        this.$api.apiList({
                            params: {
                                node: this.currentAPINode.id,
                                project: this.$route.params.id
                            }
                        }).then(resp => {
                            var tmpresult =  resp['results'];
                            for(var i = 0;i < tmpresult.length; i++ ) {
                                result.push({
                                    key: tmpresult[i].id,
                                    label: tmpresult[i].name,
                                    disabled: false
                                });
                            }
                            console.log(result);
                            return result;
                        }).catch(resp => {
                            this.$message.error({
                                message: '服务器连接超时，请重试',
                                duration: 1000
                            })
                        })
                    }
                },
                set: function(value){
                    this.transferApi = value;
                }
            },


        },
        methods: {
            handleDragEnd(){
                this.updateTree(false);
            },

            getConfig() {
                this.$api.getAllConfig(this.$route.params.id).then(resp => {
                    this.configOptions = resp;
                    this.configOptions.push({"name": "请选择", id: ''})
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            handleBackList() {
                this.addTestActivate = true;
                this.back = !this.back;
            },

            handleTestStep(resp) {
                this.testStepResp = resp;
                this.addTestActivate = false;
            },
            getTree() {
                this.$api.getTree(this.$route.params.id, {params: {type: 3}}).then(resp => {
                    this.dataTree = resp['tree'];
                    this.treeId = resp['id'];
                    this.maxId = resp['max'];
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })

                this.$api.getTree(this.$route.params.id, {params: {type: 1}}).then(resp => {
                    this.APIdataTree = resp['tree'];
                    this.APItreeId = resp['id'];
                    this.APImaxId = resp['max'];
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            updateTree(mode) {
                this.$api.updateTree(this.treeId, {
                    mode: mode,
                    body: this.dataTree,
                    node: this.currentNode.id,
                    type: 3
                }).then(resp => {
                    if (resp['success']) {
                        this.$message.success(resp['msg']);
                        this.dataTree = resp['tree'];
                        this.maxId = resp['max'];
                    } else {
                        this.$message.error(resp['msg']);
                    }
                }).catch(resp => {
                    this.$message.error({
                        message: '服务器连接超时，请重试',
                        duration: 1000
                    })
                })
            },

            deleteNode() {
                this.$confirm('节点删除操作还未完成，删除节点会导致节点下所有步骤集信息丢失，千万不要继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    if (this.currentNode === '') {
                        this.$message.info('请选择一个节点');
                    } else {
                        if (this.currentNode.children.length !== 0) {
                            this.$message.warning('此节点有子节点，不可删除！');
                        } else {
                            this.remove(this.currentNode, this.data);
                            this.updateTree(true);
                        }
                    }

                })
            },

            renameNode() {
                this.$prompt('请输入节点名', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    inputPattern: /\S/,
                    inputErrorMessage: '节点名称不能为空'
                }).then(({value}) => {
                    const parent = this.data.parent;
                    const children = parent.data.children || parent.data;
                    const index = children.findIndex(d => d.id === this.currentNode.id);
                    children[index]["label"] = value
                    this.updateTree(false);
                });
            },

            handleConfirm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.append(this.currentNode);
                        this.updateTree(false);
                        this.dialogVisible = false;
                    }
                });
            },

            handleNodeClick(node, data) {
                this.currentNode = node;
                this.data = data;
                this.suiteEditFlag = true;
            },
            handleAPINodeClick(node, data) {
                this.currentAPINode = node;
                this.APIdata = data;
            },

            filterNode(value, data) {
                if (!value) return true;
                return data.label.indexOf(value) !== -1;
            },

            remove(data, node) {
                const parent = node.parent;
                const children = parent.data.children || parent.data;
                const index = children.findIndex(d => d.id === data.id);
                children.splice(index, 1);
            },

            append(data) {
                const newChild = {id: ++this.maxId, label: this.nodeForm.name, children: []};
                if (data === '' || this.dataTree.length === 0 || this.radio === '根节点') {
                    this.dataTree.push(newChild);
                    return
                }
                if (!data.children) {
                    this.$set(data, 'children', []);
                }
                data.children.push(newChild);
            },
            //handleAPI(response) {
            //    this.addAPIFlag = true;
            //    this.response = response;
            //},
            getTestData(value){
                this.testListData=value;
            },
            saveSuite(){
                this.$refs.suiteList.testData.tests = this.$refs.apiShowList.selectedData;
                this.addstepdlgshow = false
                this.$confirm('是否立即保存刚才的修改？', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$refs.suiteList.saveSuite()
                });
            },
            handleAPI(resp){
                this.suiteEditFlag = false;
                this.response = resp;
            }
        },
        name: "Suites",
        mounted() {
            this.getTree();
            this.getConfig();
        }
    }
</script>

<style scoped>

</style>


<style>
    .el-tab-pane .el-aside{
        overflow: visible;
    }
    .el-tab-pane .el-main{
        overflow: visible;
    }
</style>
