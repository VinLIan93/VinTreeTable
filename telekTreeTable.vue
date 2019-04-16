<template>
    <div class="telekTreeTable" :style="'height:' + height">
        <div class="treeTable el-table el-table--fit el-table--border el-table--scrollable-y el-table--enable-row-hover el-table--enable-row-transition el-table--small" style="height: 100%">
            <div class="el-table__header-wrapper">
                <table class="el-table__header" cellspacing="0" cellpadding="0" border="0" style="width: 100%">
                    <thead class="has-gutter">
                        <tr>
                            <th v-for="head in namemap" class="is-center" :width="head.percent">
                                <div class="cell">{{head.name | ifUndefined}}</div>
                            </th>
                            <th class="is-center" width="20%" v-if="editted">
                                <div class="cell">操作</div>
                            </th>
                            <th class="gutter" style="width: 17px;"></th>
                        </tr>
                    </thead>
                </table>
            </div>
            <div class="el-table__body-wrapper is-scrolling-none" style="height: calc(100% - 41px)">
                <table v-for="item in tabledata" cellspacing="0" cellpadding="0" border="0" class="el-table__body" style="width: 100%">
                    <tbody>
                        <tr class="el-table__row">
                            <td v-for="(map, index) in namemap" :class=" index == 0 ? 'is-left' : 'is-center' " :width="map.percent">
                                <div v-if="map.key === 'menuIcon'" class="cell" align="left" style="padding-left: 10px">
                                    <i :class="item[map.key]" style="font-size: 18px; margin-right: 10px;"></i>{{item[map.key] | ifUndefined}}
                                </div>

                                <div v-else-if="map.key === 'menuUrl'" class="cell" align="left" style="padding-left: 10px">
                                    {{item[map.key] | ifUndefined}}
                                </div>

                                <div v-else class="cell">
                                    <span v-if="index == 0 && item[listname] && item[listname].length > 0">
                                        <i v-show="item.unShowList" class="el-icon-caret-right i_pointer" @click="item.unShowList = false"></i>
                                        <i v-show="!item.unShowList" class="el-icon-caret-bottom i_pointer" @click="item.unShowList = true"></i>
                                    </span>
                                    {{item[map.key] | ifUndefined}}
                                </div>
                            </td>
                            <td class="is-center" width="20%" v-if="editted">
                                <el-button v-if="item[uneditableKey] === undefined" type="primary" plain icon="el-icon-edit" size="mini" @click="handlerEditBtn(item)"></el-button>
                                <el-button v-if="deleteBtnShow(item)" type="danger" plain icon="el-icon-delete" size="mini" @click="handlerDeleteBtn(item)"></el-button>
                            </td>
                        </tr>
                    </tbody>
                    <tbody v-show="!item.unShowList" v-for="child in item[listname]" class="secondList">
                        <tr class="el-table__row">
                            <td v-for="(map, index) in namemap" :class=" index == 0 ? 'is-left' : 'is-center' " :width="map.percent">
                                <div v-if="map.key === 'menuIcon'" class="cell" align="left" style="padding-left: 10px">
                                    <i :class="child[map.key]" style="font-size: 18px; margin-right: 10px;"></i>{{child[map.key] | ifUndefined}}
                                </div>

                                <div v-else-if="map.key === 'menuUrl'" class="cell" align="left" style="padding-left: 10px">
                                    {{child[map.key] | ifUndefined}}
                                </div>

                                <div v-else class="cell">
                                    <span v-if="index == 0 && child[listname] && child[listname].length > 0">
                                        <i v-show="child.unShowList" class="el-icon-caret-right i_pointer" @click="child.unShowList = false"></i>
                                        <i v-show="!child.unShowList" class="el-icon-caret-bottom i_pointer" @click="child.unShowList = true"></i>
                                    </span>
                                    {{child[map.key] | ifUndefined}}
                                </div>
                            </td>
                            <td class="is-center" width="20%" v-if="editted">
                                <el-button v-if="child[uneditableKey] === undefined" type="primary" plain icon="el-icon-edit" size="mini" @click="handlerEditBtn(child)"></el-button>
                                <el-button v-if="deleteBtnShow(child)" type="danger" plain icon="el-icon-delete" size="mini" @click="handlerDeleteBtn(child)"></el-button>
                            </td>
                        </tr>

                        <tr v-show="!child.unShowList" v-for="third in child[listname]" class="table__row thirdList">
                            <td v-for="(map, index) in namemap" :class=" index == 0 ? 'is-left' : 'is-center' " :width="map.percent">
                                <div v-if="map.key === 'menuIcon'" class="cell" align="left" style="padding-left: 10px">
                                    <i :class="third[map.key]" style="font-size: 18px; margin-right: 10px;"></i>{{third[map.key] | ifUndefined}}
                                </div>

                                <div v-else-if="map.key === 'menuUrl'" class="cell" align="left" style="padding-left: 10px">
                                    {{third[map.key] | ifUndefined}}
                                </div>

                                <div v-else class="cell">
                                    {{third[map.key] | ifUndefined}}
                                </div>
                            </td>
                            <td class="is-center" width="20%" v-if="editted">
                                <el-button v-if="third[uneditableKey] === undefined" type="primary" plain icon="el-icon-edit" size="mini" @click="handlerEditBtn(third)"></el-button>
                                <el-button v-if="deleteBtnShow(third)" type="danger" plain icon="el-icon-delete" size="mini" @click="handlerDeleteBtn(third)"></el-button>
                            </td>
                        </tr>

                    </tbody>
                </table>
            </div>



        </div>
    </div>
</template>

<script>

export default {
    name: 'telekTreeTable',
    //注册组件
    components: {},
    //属性传值
    props: {
        height: {
            type: String,
            default: '100%'
        },
        tabledata: Array,
        namemap: Array,
        listname: String,
        uneditableKey: {
            type: String,
            default: ''
        },
        undeleteKey: {
            type: String,
            default: ''
        },
        undelete: {
            type: Boolean,
            default: false
        },
        editted: {
            type: Boolean,
            default: true
        }
    },
    //数据
    data() {
		return {}
	},
    //计算属性
    computed: {},
    //方法
    methods: {
        handlerEditBtn(Obj) {
            this.$emit('handlerEditBtn', Obj)
        },
        handlerDeleteBtn(Obj) {
            const self = this;
            self.$confirm('此操作将删除选中项, 是否继续？', '警告', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$emit('handlerDeleteBtn', Obj)
            }).catch(() => {
                return false;
            })
        },
        deleteBtnShow(row) {
            if(row[this.uneditableKey] === undefined && this.undelete === false) {
                if(this.undeleteKey !== undefined && this.undeleteKey !== '') {
                    if(row[this.undeleteKey] === '') {
                        return true
                    }else{
                        return false
                    }
                }else{
                    return true
                }
            }else{
                return false
            }
        }
    },
    //过滤器
    filters: {
        ifUndefined(value) {
            if(value === undefined) {
                return '';
            }
            return value;
        }
    },
    //生命周期钩子——组件实例刚被创建，组件属性计算之前
    beforeCreate() {},
    //生命周期钩子——组件实例创建完成，属性已绑定，但DOM未生成
    created() {},
    //生命周期钩子——完成了 el 和 data 初始化，模拟编译/挂载之前
    beforeMount() {},
    //生命周期钩子——完成了 el 和 data 初始化，模拟编译/挂载完成
    mounted() {},
    //生命周期钩子——组件更新之前
    beforeUpdate() {},
    //生命周期钩子——组件更新之后
    updated() {},
    //生命周期钩子——组件销毁前调用
    beforeDestroy() {},
    //生命周期钩子——组件销毁后调用
    destroyed() {},
    //侦听器
    watch: {}

}


</script>

<style>
.telekTreeTable{
    height: 100%;
}
.telekTreeTable .secondList .is-left .cell{
    margin-left: 40px;
}
.telekTreeTable .thirdList .is-left .cell{
    margin-left: 80px;
}
.telekTreeTable .el-table--scrollable-y .el-table__body-wrapper{
    overflow-y: scroll;
}
.telekTreeTable .i_pointer{
    cursor: pointer;
}
</style>


