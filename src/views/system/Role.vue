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
                <FormItem label="权限" prop="moduleIds">
                    <Tree v-model="updateForm.moduleIds" ref="updateModules" :data="data4" multiple show-checkbox></Tree>
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
                <FormItem label="权限" prop="moduleIds">

                    <Tree v-model="addForm.moduleIds" ref="addModules" :data="data3" :load-data="getAllModules" show-checkbox></Tree>

                </FormItem>

            </Form>
        </Modal>
    </div>
</template>

<script type="text/ecmascript-6">
    import fetch from 'utils/fetch';

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
                }
            }
        },
        methods: {
            addRole(){
                this.modal2=true;
                //初始化tree
                fetch({
                    url: '/system/module/getChildren/0',
                    method: 'get'
                }).then((result) => {
                    this.data3 = result.data;
                });
            },
            add(){

                const role = this.addForm;
                //vue官网 组件-子组件引用章节
                const selectedNodes = this.$refs.addModules.getCheckedNodes();
                selectedNodes.forEach((node,i)=>{
                    this.addForm.moduleIds += node.id;
                    if(i+1<selectedNodes.length){
                        this.addForm.moduleIds +=",";
                    }
                });
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
                    url: '/system/module/getChildren/0/'+roleId,
                    method: 'get'
                }).then((result) => {
                    this.data4 = result.data;
                    this.modal1=true;
                });


            },
            update (name) {

                const role = this.updateForm;
                //vue官网 组件-子组件引用章节
                const selectedNodes = this.$refs.updateModules.getCheckedNodes();
                selectedNodes.forEach((node,i)=>{
                    this.updateForm.moduleIds += node.id;
                    if(i+1<selectedNodes.length){
                        this.updateForm.moduleIds +=",";
                    }
                });
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
        created:function(){
            this.gopage(this.pageNo);





        }
    }
</script>