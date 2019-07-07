<template>
    <el-table
        strpe
        height1="460"
        :data="allextract"
        style="width: 100%;"
        @cell-mouse-enter="cellMouseEnter"
        @cell-mouse-leave="cellMouseLeave"
        :cell-style="{paddingTop: '4px', paddingBottom: '4px'}"
    >
        <el-table-column
            label="变量名"
            width="300">
            <template slot-scope="scope">
                <el-input clearable v-model="scope.row.key" placeholder="接收抽取值后的变量名" :disabled="scope.row.disabled"></el-input>
            </template>
        </el-table-column>
        <el-table-column
            label="抽取表达式"
            width="420">
            <template slot-scope="scope">
                <el-input clearable v-model="scope.row.value" placeholder="抽取表达式" :disabled="scope.row.disabled"></el-input>

            </template>
        </el-table-column>

        <el-table-column
            label="描述"
            width="200">
            <template slot-scope="scope">
                <el-input clearable v-model="scope.row.desc" placeholder="抽取值简要描述" :disabled="scope.row.disabled"></el-input>
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
            extract: {
                require: false
            },
            srcextract: {
                require: false
            }
        },

        computed:{
            allextract: function() {
                var tmp = [];
                for(var i=0;i<this.srcextract.length;i++){
                    this.srcextract[i]['disabled'] = true
                    tmp.push(this.srcextract[i])
                }
                for(var i=0;i<this.extract.length;i++){
                    this.extract[i]['disabled'] = false
                    tmp.push(this.extract[i])
                }
                if(this.extract.length === 0){
                    tmp.push({key: '', value: '', desc: '',disabled: false })
                }
                return tmp;
            }
        },
        watch: {
            save: function () {
                this.$emit('extract', this.parseExtract());
            },
            allextract: function () {
                if (this.allextract.length !== 0) {
                    this.tableData = this.allextract;
                }
            }
        },

        methods: {
            cellMouseEnter(row) {
                this.currentRow = row;
            },

            cellMouseLeave(row) {
                this.currentRow = '';
            },

            handleEdit(index, row) {
                this.allextract.push({
                    key: '',
                    value: '',
                    desc: ''
                });
            },

            handleDelete(index, row) {
                this.allextract.splice(index, 1);
                let x=true;
                var each;
                for(each in this.allvalidate){
                    if(each['disabled']===false){
                        x=false;
                    }
                }
                if(x){
                    this.allextract.push({
                        expect: '',
                        actual: '',
                        comparator: 'equals',
                        type: 1
                    });
                }
            },
            // 抽取格式化
            parseExtract() {
                let extract = {
                    extract: [],
                    desc: {}
                };
                for (let content of this.allextract) {
                    const key = content['key'] ;
                    const value = content['value'];
                    if (key !== '' && value !== '' && content['disabled'] !== true) {
                        let obj = {};
                        obj[key] = value;
                        extract.extract.push(obj);
                        extract.desc[key] = content['desc'];
                    }
                }
                return extract;
            }
        },

        data() {
            return {
                currentRow: '',
                tableData: [{
                    key: '',
                    value: '',
                    desc: ''
                }]
            }
        },
        name: "Extract"
    }
</script>

<style scoped>
</style>
