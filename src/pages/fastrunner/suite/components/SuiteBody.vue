<template>
    <div>
        <div>
            <div>
                <el-input
                    style="width: 600px"
                    placeholder="请输入接口名称"
                    v-model="name"
                    clearable
                    :disabled = "true"
                >
<!--                    onkeyup="value=value.replace(/[\a-\z\A-\Z\_]/g,'')"-->
                    <template slot="prepend">接口信息录入</template>

                    <el-button
                        slot="append"
                        type="success"
                        plain
                        @click="save = !save"
                    >Save
                    </el-button>
                </el-input>

                <el-button
                    type="primary"
                    @click="reverseStatus"
                    v-loading="loading"
                    :disabled="false"
                >Send
                </el-button>
            </div>

            <div>
                <el-input
                    style="width: 600px; margin-top: 10px"
                    placeholder="请输入接口请求地址"
                    v-model="url"
                    clearable
                    :disabled="true"
                >
                    <el-select
                        style="width: 125px"
                        slot="prepend"
                        v-model="method"
                        :disabled="true"
                    >
                        <el-option
                            v-for="item of httpOptions"
                            :label="item.label"
                            :value="item.label"
                            :key="item.value"
                        >
                        </el-option>
                    </el-select>

                </el-input>

                <el-tooltip
                    effect="dark"
                    content="循环次数"
                    placement="bottom"
                >
                    <el-input-number
                        v-model="times"
                        controls-position="right"
                        :min="1"
                        :max="100"
                        style="width: 120px"
                        :disabled="true"
                    >
                    </el-input-number>
                </el-tooltip>
            </div>

        </div>

        <div class="request" style="height:600px">

            <el-dialog
                v-if="dialogTableVisible"
                :visible.sync="dialogTableVisible"
                width="70%"
            >
                <new-debug-report :summary="summary"></new-debug-report>
            </el-dialog>

            <el-collapse
                style="margin-left: 20px;"
                v-model="activeTag"
            >
                <el-collapse-item title="Headers httprunner原生并不支持header的覆写" name="first">
                    <headers
                        :save="save"
                        v-on:headers="handleHeaders"
                        :srcheaders="response ? response.apiStep.headers: [] "
                        :headers=[]>
                        <!--:headers="response ? response.suiteStep.headers: [] ">-->
                    </headers>
                </el-collapse-item>

                <el-collapse-item title="Request httprunner原生并不支持request的覆写" name="second">
                    <request
                        :save="save"
                        v-on:request="handleRequest"
                        :srcrequest="response ? response.apiStep.request: []"
                        :request="response ? response.suiteStep.request: []"
                    >
                    </request>
                </el-collapse-item>

                <el-collapse-item title="Extract" name="third">
                    <extract
                        :save="save"
                        v-on:extract="handleExtract"
                        :srcextract="response ? response.apiStep.extract : []"
                        :extract="response ? response.suiteStep.extract : []"
                    >
                    </extract>
                </el-collapse-item>

                <el-collapse-item title="Validate" name="fourth">
                    <validate
                        :save="save"
                        v-on:validate="handleValidate"
                        :srcvalidate="response ? response.apiStep.validate: []"
                        :validate="response ? response.suiteStep.validate: []"
                    >

                    </validate>
                </el-collapse-item>

                <el-collapse-item title="Variables" name="five">
                    <variables
                        :save="save"
                        v-on:variables="handleVariables"
                        :srcvariables="response ? response.apiStep.variables : []"
                        :variables="response ? response.suiteStep.variables : []"
                    >

                    </variables>
                </el-collapse-item>

                <el-collapse-item title="Hooks 暂时不支持" name="six">
                    <hooks
                        :save="save"
                        v-on:hooks="handleHooks"
                        :hooks="response ? response.suiteStep.hooks: []"
                    >
                    </hooks>
                </el-collapse-item>
            </el-collapse>

        </div>


    </div>

</template>

<script>
    import Headers from '../../../httprunner/newcomponents/Headers'
    import Request from '../../../httprunner/newcomponents/Request'
    import Extract from '../../../httprunner/newcomponents/Extract'
    import Validate from '../../../httprunner/newcomponents/Validate'
    import Variables from '../../../httprunner/newcomponents/Variables'
    import Hooks from '../../../httprunner/newcomponents/Hooks'
    import newDebugReport from '../../../reports/newDebugReport'

    export default {
        components: {
            Headers,
            Request,
            Extract,
            Validate,
            Variables,
            Hooks,
            newDebugReport

        },

        props: {
            nodeId: {
                require: false
            },
            project: {
                require: false
            },
            config:{
                require:true
            },
            response: {
                require: false
            }
        },
        methods: {
            reverseStatus() {
                this.save = !this.save;
                this.run = true;
            },

            handleHeaders(headers) {
                this.headers = headers;
            },
            handleRequest(request) {
                //this.request = request;
            },
            handleValidate(validate) {
                this.validate = validate;
            },
            handleExtract(extract) {
                this.extract = extract;
            },
            handleVariables(variables) {
                this.variables = variables;
            },
            handleHooks(hooks) {
                //this.hooks = hooks;

                if (!this.run) {
                    this.updateSuiteAPI();
                } else {
                    this.runAPI();
                    this.run = false;
                }
            },

            validateData() {
                if (this.url === '') {
                    this.$notify.error({
                        title: 'url错误',
                        message: '接口请求地址不能为空',
                        duration: 1500
                    });
                    return false;
                }

                if (this.name === '') {
                    this.$notify.error({
                        title: 'name错误',
                        message: '接口名称不能为空',
                        duration: 1500
                    });
                    return false;
                }
                return true
            },
            updateSuiteAPI() {
                if (this.validateData()) {
                    this.$api.updateSuiteStep({
                        relation:this.nodeId,
                        project:this.project,
                        srcindex:this.srcindex,
                        extract: this.extract,
                        validate: this.validate,
                        variables: this.variables,
                        hooks: this.hooks,
                        url: this.url,
                        method: this.method,
                        name: this.name,
                        times: this.times,
                    }).then(resp => {
                        if (resp.success) {
                            this.$message.success({
                                message: '接口更新成功',
                                duration: 1000
                            });
                            this.$emit('addSuccess');
                        } else {
                            this.$message.error({
                                message: resp.msg,
                                duration: 1000
                            })
                        }
                    }).catch(resp => {
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        })
                    })
                }
            },
            runAPI() {
                if (this.validateData()) {
                    this.loading = true;
                    this.$api.runDebugSuiteStep({
                        headers: this.headers,
                        request: this.request,
                        extract: this.extract,
                        validate: this.validate,
                        variables: this.variables,
                        hooks: this.hooks,
                        url: this.url,
                        method: this.method,
                        name: this.name,
                        times: this.times,
                        project: this.project,
                        config: this.config,
                        apiId: this.apiId,
                        srcName:this.srcName
                    }).then(resp => {
                        this.summary = resp;
                        this.dialogTableVisible = true;
                        this.loading = false;
                    }).catch(resp => {
                        this.$message.error({
                            message: '服务器连接超时，请重试',
                            duration: 1000
                        });
                        this.loading = false;
                    })
                }
            },
        },
        computed: {
            name:function(){ return this.response.name;},
            method:function(){ return this.response.method;},
            url:function(){ return this.response.url;},
            srcindex:function(){ return this.response.srcindex;},
            apiId:function(){ return this.response.apiId;},
            srcName:function(){ return this.response.srcName;},
        },
        data() {
            return {
                loading: false,
                times: 1,
                id: '',
                headers: [],
                request: [],
                extract: [],
                validate: [],
                variables: [],
                hooks: [],
                dialogTableVisible: false,
                save: false,
                run: false,
                summary: {},
                activeTag: '',
                httpOptions: [{
                    label: 'GET',
                }, {
                    label: 'POST',
                }, {
                    label: 'PUT',
                }, {
                    label: 'DELETE',
                }, {
                    label: 'HEAD',
                }, {
                    label: 'OPTIONS',
                }, {
                    label: 'PATCH',
                }],
            }
        },
        name: "SuiteBody"
    }
</script>

<style scoped>
    .request {
        margin-top: 15px;
        border: 1px solid #ddd;
    }


</style>
