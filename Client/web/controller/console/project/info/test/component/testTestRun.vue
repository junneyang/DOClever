<template>
    <el-dialog title="选择用例" width="50%" ref="box" :visible.sync="showDialog" append-to-body>
        <el-row class="row" style="text-align: center;height: 50px;line-height: 50px">
            <el-col class="col" :span="4">
                测试用例
            </el-col>
            <el-col class="col" :span="20">
                <el-cascader size="small" expand-trigger="hover" :options="arrTest" v-model="arrSelTest" style="width: 90%" filterable placeholder="请选择测试用例">
                </el-cascader>
            </el-col>
        </el-row>
        <el-row class="dialog-footer" slot="footer">
            <el-button type="primary" @click="save">
                保存
            </el-button>
        </el-row>
    </el-dialog>
</template>

<script>
    module.exports={
        props:["source","test","self"],
        data:function () {
            return {
                arrTest:function () {
                    var _this=this;
                    return this.source.map(function (obj) {
                        var arr=obj.data.map(function (obj) {
                            var arr=obj.data.map(function (obj) {
                                var ret={
                                    label:obj.name,
                                    value:obj.id,
                                };
                                if(_this.self && _this.self==obj.id)
                                {
                                    ret.disabled=true;
                                }
                                return ret;
                            })
                            return {
                                label:obj.name,
                                value:obj.id,
                                children:arr
                            }
                        })
                        return {
                            label:obj.name,
                            value:obj.id,
                            children:arr
                        }
                    })
                }.call(this),
                arrSelTest:function () {
                    if(this.test)
                    {
                        var module,group,id,_this=this;
                        this.source.forEach(function (obj) {
                            var _obj=obj;
                            obj.data.forEach(function (obj) {
                                var __obj=obj;
                                obj.data.forEach(function (obj) {
                                    if(obj.id==_this.test.id)
                                    {
                                        module=_obj.id;
                                        group=__obj.id;
                                        id=obj.id;
                                    }
                                })
                            })
                        })
                        if(module && group && id)
                        {
                            return [module,group,id];
                        }
                        else
                        {
                            $.tip("用例已不存在",0);
                            return [];
                        }
                    }
                    else
                    {
                        return []
                    }
                }.call(this),
                showDialog:false
            }
        },
        computed:{

        },
        methods:{
            save:function () {
                if(this.arrSelTest.length!=3)
                {
                    $.tip("请选择用例",0);
                    return
                }
                var name,_this=this;
                this.source.forEach(function (obj) {
                    if(obj.id==_this.arrSelTest[0])
                    {
                        obj.data.forEach(function (obj) {
                            if(obj.id==_this.arrSelTest[1])
                            {
                                obj.data.forEach(function (obj) {
                                    if(obj.id==_this.arrSelTest[2])
                                    {
                                        name=obj.name
                                    }
                                })
                            }
                        })
                    }
                })
                this.$emit("save",{
                    name:name,
                    id:this.arrSelTest[2]
                });
                this.showDialog=false;
            }
        }
    }
</script>
