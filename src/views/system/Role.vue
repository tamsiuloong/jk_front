<template>


    <div class="animated fadeIn">
        <!--<Icon type="android-add"></Icon>-->
        <div id="container">
            <Icon type="android-add-circle"></Icon>
            <i-button type="error" @click="addRole">添加角色</i-button>
        </div>
        <br>
        <Table :columns="columns1" :data="data1"></Table>
        <Page :total="totalCount" :page-size="pageSize" :current="pageNo" @on-change="gopage" align="center"></Page>
        <Modal
                v-model="modal1"
                title="编辑角色"
                @on-ok="update"
                @on-cancel="cancel" width="60%">
            <Form ref="updateForm"  :model="updateForm" :rules="ruleCustom" :label-width="80">
                <Input type="hidden" v-model="updateForm.id"/>
                <FormItem label="角色名字" prop="name">
                    <Input type="text" v-model="updateForm.name"/>
                </FormItem>
                <!--<FormItem label="权限" prop="moduleIds">-->
                    <!--<Tree v-model="updateForm.moduleIds" ref="updateModules" :data="data4" multiple show-checkbox></Tree>-->
                <!--</FormItem>-->

                <FormItem label="权限" prop="moduleIds">
                    <ul id="updateTree" class="ztree"></ul>
                </FormItem>
            </Form>
        </Modal>
        <Modal
                v-model="modal2"
                title="添加角色"
                @on-ok="add"
                @on-cancel="cancel" width="60%">
            <Form ref="addForm" :model="addForm" :rules="ruleCustom" :label-width="80">
                <FormItem label="角色名字" prop="name">
                    <Input type="text" v-model="addForm.name"/>
                </FormItem>
                <Input type="hidden" v-model="addForm.moduleIds"/>
                <!--<FormItem label="权限" prop="moduleIds">-->

                    <!--<Tree v-model="addForm.moduleIds" ref="addModules" :data="data3" :load-data="getAllModules" show-checkbox></Tree>-->

                <!--</FormItem>-->
                <FormItem label="权限" prop="moduleIds">
                    <ul id="addTree" class="ztree"></ul>
                </FormItem>
            </Form>
        </Modal>
    </div>
</template>

<script type="text/ecmascript-6">
    import fetch from 'utils/fetch';
    import "../../../static/ztree/js/jquery-1.4.4.min.js"
    import "../../../static/ztree/js/jquery.ztree.core.min.js"
    import "../../../static/ztree/js/jquery.ztree.excheck.min.js"

    export default {
        data() {
            const validateUser = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入角色名'));
                }
            };
            const validateAddr = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入地址'));
                }
            };
            const validateid = (rule, value, callback) => {
                if (value==='') {
                    return callback(new Error('年龄不能为空'));
                }
                // 模拟异步验证效果
                setTimeout(() => {
                    if (!Number.isInteger(value)) {
                        callback(new Error('请输入整数'));
                    } else {
                        if (value < 18) {
                            callback(new Error('必须年满18！'));
                        } else {
                            callback();
                        }
                    }
                }, 1000);
            };
            return {
                setting:{
                    check: {
                        enable: true
                    },
                    data: {
                        simpleData: {
                            enable: true
                        }
                    }
                },zNodes:[
                    { id:1, pId:0, name:"随意勾选 1", open:true},
                    { id:11, pId:1, name:"随意勾选 1-1", open:true},
                    { id:111, pId:11, name:"随意勾选 1-1-1"},
                    { id:112, pId:11, name:"随意勾选 1-1-2"},
                    { id:12, pId:1, name:"随意勾选 1-2", open:true},
                    { id:121, pId:12, name:"随意勾选 1-2-1"},
                    { id:122, pId:12, name:"随意勾选 1-2-2"},
                    { id:2, pId:0, name:"随意勾选 2", checked:true, open:true},
                    { id:21, pId:2, name:"随意勾选 2-1"},
                    { id:22, pId:2, name:"随意勾选 2-2", open:true},
                    { id:221, pId:22, name:"随意勾选 2-2-1", checked:true},
                    { id:222, pId:22, name:"随意勾选 2-2-2"},
                    { id:23, pId:2, name:"随意勾选 2-3"}
                ],
                tempIndex:0,
                pageSize:20,
                pageNo:1,
                totalPage:0,
                totalCount:0,
                columns1: [
                    {
                        title: '编号',
                        key: 'id'
                    },
                    {
                        title: '角色名字',
                        key: 'name'
                    },
                    {
                        title: '操作',
                        key: 'action',
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
//                                h('Button', {
//                                    props: {
//                                        type: 'primary',
//                                        size: 'small'
//                                    },
//                                    style: {
//                                        marginRight: '5px'
//                                    },
//                                    on: {
//                                        click: () => {
//                                            this.show(params.index)
//                                        }
//                                    }
//                                }, '查看'),
                                h('Button', {
                                    props: {
                                        type: 'primary',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                            this.edit(params.index)
                                        }
                                    }
                                }, '编辑'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.remove(params.index)
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],
                self: this,
                data1: [

                ],
                modal1: false,
                modal2: false,
                updateForm: {
                    id:'',
                    name: '',
                    moduleIds:""
                },
                addForm: {
                    name: '',
                    moduleIds:""
                },data3: [

                ],
                ruleCustom: {
                    name: [
                        { validator: validateUser, trigger: 'blur' }
                    ],
                    id: [
                        { validator: validateid, trigger: 'blur' }
                    ]
                },
                zTreeObj:{}
            }
        },
        methods: {
            addRole(){
                this.modal2=true;

            },
            add(){

                const role = this.addForm;
                //vue官网 组件-子组件引用章节
//                const selectedNodes = this.$refs.addModules.getCheckedNodes();
//                selectedNodes.forEach((node,i)=>{
//                    this.addForm.moduleIds += node.id;
//                    if(i+1<selectedNodes.length){
//                        this.addForm.moduleIds +=",";
//                    }
//                });

                const nodes = this.zTreeObj.getCheckedNodes(true);		//取得选中的结点
                var str = "";
                for (let i = 0; i < nodes.length; i++) {
                    if (str != "") {
                        str += ",";
                    }
                    str += nodes[i].id;
                }

                this.addForm.moduleIds=str;
                fetch({
                    url: '/system/role',
                    method: 'post',
                    params:role
                }).then((result) => {
                    this.data1.unshift(result.data);
                    this.$Message.success('Success!');
                });

            },
            getAllModules (item, callback) {
                setTimeout(() => {

                    fetch({
                        url: '/system/module/getChildren/'+item.id,
                        method: 'get'
                    }).then((result) => {
                        callback(result.data);
                    });


                }, 500);
            },
            show (index) {
                this.$Modal.info({
                    title: '角色信息',
                    content: `姓名：${this.data1[index].name}`
                })
            },
            remove (index) {
                const id = this.data1[index].id;
                fetch({
                    url: '/system/role',
                    method: 'delete',
                    params:{ids:id}
                }).then((result) => {
                    if(result.data=='1')
                    {
                        this.$Message.success('Success!');
                        this.data1.splice(index, 1);
                    }
                });
            },
            ok () {
                this.modal1 = false,
                        this.$Message.info('点击了确定');
            },
            cancel () {
                this.$Message.info('点击了取消');
            },
            edit (index) {
                this.$refs.updateForm.resetFields();
                this.tempIndex=index;
                this.updateForm.name=this.data1[index].name;
                this.updateForm.id=this.data1[index].id;
                const  roleId = this.data1[index].id;
                //初始化tree
                fetch({
                    url: '/system/module/getAllModules/'+roleId,
                    method: 'get'
                }).then((result) => {
//                    this.data4 = result.data;
                    this.modal1=true;
                    this.zTreeObj = $.fn.zTree.init($("#updateTree"), this.setting, result.data);
                });


            },
            update (name) {

                const role = this.updateForm;
                //vue官网 组件-子组件引用章节
//                const selectedNodes = this.$refs.updateModules.getCheckedNodes();
//                selectedNodes.forEach((node,i)=>{
//                    this.updateForm.moduleIds += node.id;
//                    if(i+1<selectedNodes.length){
//                        this.updateForm.moduleIds +=",";
//                    }
//                });



                const nodes = this.zTreeObj.getCheckedNodes(true);		//取得选中的结点
                var str = "";
                for (let i = 0; i < nodes.length; i++) {
                    if (str != "") {
                        str += ",";
                    }
                    str += nodes[i].id;
                }

                this.updateForm.moduleIds=str;
                fetch({
                    url: '/system/role',
                    method: 'put',
                    params:role
                }).then((result) => {
                    this.$Message.success('Success!');
                });
            },
            handleReset (name) {
                this.$refs[name].resetFields();
            },
            gopage(pageNo){
                const pageSize = this.pageSize;
                fetch({
                    url: '/system/role',
                    method: 'get',
                    params:{pageNo,pageSize}
                }).then((result) => {
                    this.data1=result.data.list;
                    this.pageNo=pageNo;
                    this.pageSize=pageSize;
                    this.totalCount=result.data.totalCount;
                });
            }
        },
        mounted:function(){
            this.gopage(this.pageNo);


            //初始化addTree
            fetch({
                url: '/system/module/getAllModules',
                method: 'get'
            }).then((result) => {
                // this.data3 = result.data;
                this.zTreeObj = $.fn.zTree.init($("#addTree"), this.setting, result.data);
            });


        }
    }
</script>