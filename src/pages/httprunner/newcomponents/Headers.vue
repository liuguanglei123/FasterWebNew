<template>
    <el-table
        height1="460"
        :data="tableData"
        style="width: 100%;"
        :border="false"
        @cell-mouse-enter="cellMouseEnter"
        @cell-mouse-leave="cellMouseLeave"
        :cell-style="{paddingTop: '4px', paddingBottom: '4px'}"
    >
        <el-table-column
            label="标签"
            width="300">
            <template slot-scope="scope">
                <el-autocomplete
                    clearable
                    v-model="scope.row.key"
                    :fetch-suggestions="querySearch"
                    placeholder="头部标签"
                    :disabled="scope.row.disabled"
                >
                </el-autocomplete>
            </template>
        </el-table-column>

        <el-table-column
            label="内容"
            width="400">
            <template slot-scope="scope">
                <el-input clearable v-model="scope.row.value" placeholder="头部内容" :disabled="scope.row.disabled"></el-input>
            </template>
        </el-table-column>

        <el-table-column
            label="描述"
            width="220">
            <template slot-scope="scope">
                <el-input clearable v-model="scope.row.desc" placeholder="头部信息简要描述" :disabled="scope.row.disabled"></el-input>

            </template>
        </el-table-column>

        <el-table-column
            width="130">
            <template slot-scope="scope">
                <el-row v-show="scope.row === currentRow && !scope.row.disabled">
                    <el-button
                        icon="el-icon-circle-plus-outline"
                        size="mini"
                        type="info"
                        @click="handleEdit(scope.$index, scope.row)">
                    </el-button>

                    <el-button
                        icon="el-icon-delete"
                        size="mini"
                        type="danger"
                        v-show="scope.$index !== 0"
                        @click="handleDelete(scope.$index, scope.row)">
                    </el-button>
                </el-row>

            </template>
        </el-table-column>
    </el-table>

</template>

<script>

    export default {
        props: {
            save: Boolean,
            headers: {
                require: false
            },
            srcheaders:{
                require: false
            }
        },
        computed:{
            allheaders: function() {
                var tmp = [];
                for(var i=0;i<this.srcheaders.length;i++){
                    this.srcheaders[i]['disabled'] = true
                    tmp.push(this.srcheaders[i])
                }
                for(var i=0;i<this.headers.length;i++){
                    this.headers[i]['disabled'] = false
                    tmp.push(this.headers[i])
                }
                /*if(this.headers.length === 0){
                    tmp.push({key: '', value: '', desc: '',disabled: false })
                }*/
                return tmp;
            }
        },
        methods: {
            querySearch(queryString, cb) {
                let headerOptions = this.headerOptions;
                let results = queryString ? headerOptions.filter(this.createFilter(queryString)) : headerOptions;
                cb(results);
            },

            createFilter(queryString) {
                return (headerOptions) => {
                    return (headerOptions.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0);
                };
            },

            cellMouseEnter(row) {
                this.currentRow = row;
            },

            cellMouseLeave(row) {
                this.currentRow = '';
            },

            handleEdit(index, row) {
                /*this.tableData.push({
                    key: '',
                    value: '',
                    desc: ''
                });*/
            },

            handleDelete(index, row) {
                this.tableData.splice(index, 1);
            },

            // 头部信息格式化
            parseHeader() {
                let headers = {
                    headers: {},
                    desc: {}
                };
                for (let content of this.tableData) {
                    if (content['key'] !== '' && content['value'] !== '' && content['disabled'] !== true) {
                        headers.headers[content['key']] = content['value'];
                        headers.desc[content['key']] = content['desc'];
                    }
                }
                return headers;
            }
        },
        watch: {
            save: function () {
                this.$emit('headers', this.parseHeader(), this.tableData);
            },

            allheaders: function () {
                if (this.allheaders.length !== 0) {
                    this.tableData = this.allheaders;
                }
            }
        },
        data() {
            return {
                headerOptions: [{
                    value: 'Accept'
                }, {
                    value: 'Accept-Charset'
                }, {
                    value: 'Accept-Language'
                }, {
                    value: 'Accept-Datetime'
                }, {
                    value: 'Authorization'
                }, {
                    value: 'Cache-Control'
                }, {
                    value: 'Connection'
                }, {
                    value: 'Cookie'
                }, {
                    value: 'Content-Length'
                }, {
                    value: 'Content-MD5'
                }, {
                    value: 'Content-Type'
                }, {
                    value: 'Expect'
                }, {
                    value: 'Date'
                }, {
                    value: 'From'
                }, {
                    value: 'Host'
                }, {
                    value: 'If-Match'
                }, {
                    value: 'If-Modified-Since'
                }, {
                    value: 'If-None-Match'
                }, {
                    value: 'If-Range'
                }, {
                    value: 'If-Unmodified-Since'
                }, {
                    value: 'Max-Forwards'
                }, {
                    value: 'Origin'
                }, {
                    value: 'Pragma'
                }, {
                    value: 'Proxy-Authorization'
                }, {
                    value: 'Range'
                }, {
                    value: 'Referer'
                }, {
                    value: 'TE'
                }, {
                    value: 'User-Agent'
                }, {
                    value: 'Upgrade'
                }, {
                    value: 'Via'
                }, {
                    value: 'Warning'
                }],

                currentRow: '',
                tableData: [],
            }
        },
        name: "Headers"
    }
</script>

<style scoped>
</style>
