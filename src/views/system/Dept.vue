<template>


    <div class="animated fadeIn">
        <div>
            <Row style="margin-bottom: 25px;">
                <Col span="8">部门名字：
                <Input v-model="deptNo" placeholder="请输入..." style="width:200px"/>
                </Col>
                <Col span="8"><Button type="primary" shape="circle" icon="ios-search" @click="search()">搜索</Button></Col>
            </Row>
        </div>
        <div>
            <ul>
                <li>
                    <Button type="primary" icon="plus-round" @click="addDept()">新建</Button>
                    <Button type="success" icon="wrench" @click="edit()">修改</Button>
                    <Button type="error" icon="trash-a" @click="remove()">删除</Button>
                </li>
                <li>
                    <div style="padding: 10px 0;">
                        <Table border :columns="columns1" :data="data1" @on-selection-change="s=>{change(s)}"></Table>
                    </div>
                </li>
                <li>
                    <div style="text-align: right;">
                        <Page :total="totalCount" :page-size="pageSize" :current="pageNo" @on-change="gopage"
                              align="center"></Page>
                    </div>
                </li>
            </ul>
        </div>


        <Modal
                v-model="modal1"
                title="编辑部门"
                width="60%"
                :mask-closable="false"
                :loading="loading"
                @on-ok="update"
                @on-cancel="cancel">
            <Form ref="updateForm" :model="updateForm" :rules="ruleCustom" :label-width="80">

                <Row>
                    <Col span="11">
                    <FormItem label="部门名字" prop="deptName">
                        <Input type="text" v-model="updateForm.deptName"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="状态" prop="state">
                        <RadioGroup v-model="updateForm.state" type="button">
                            <Radio label="0">不可用</Radio>
                            <Radio label="1">可用</Radio>
                        </RadioGroup>
                    </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <Modal
                v-model="modal2"
                title="添加部门"
                :mask-closable="false"
                :loading="loading"
                @on-ok="add"
                @on-cancel="cancel" width="60%">
            <Form  ref="addForm" :model="addForm" :rules="ruleCustom"  :label-width="80">
                <Row>
                    <Col span="11">
                    <FormItem label="部门名字" prop="deptName">
                        <Input type="text" v-model="addForm.deptName"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">

                    </Col>
                </Row>
            </Form>
        </Modal>
    </div>
</template>

<script type="text/ecmascript-6">
    import fetch from 'utils/fetch';
    import {dateFormat} from 'utils/date';

    export default {
        data() {
            return {
                loading:true,
                count: 0,
                gourpId: null,
                pageSize: 20,
                pageNo: 1,
                totalPage: 0,
                totalCount: 0,
                deptNo:"",
                columns1: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '部门名字',
                        key: 'deptName'
                    },
                    {
                        title: '状态',
                        key: 'state',
                        render: (h, params) => {
                            const task_status = parseInt(params.row.state);
                            if (task_status === 0) {
                                return "不可用";
                            }
                            else if (task_status === 1) {
                                return "可用";
                            }
                            else {
                                return "unkown"
                            }
                        }
                    }
                ],
                self: this,
                data1: [],
                modal1: false,
                modal2: false,
                updateForm: {
                    id:"",
                    deptName:"",
                    parentId:"",
                    state:""
                },
                addForm: {
                    deptName:"",
                    parentId:"",
                    state:"1"
                },
                ruleCustom: {
                    deptName: [
                        {required: true, message:'不能为空',trigger:'blur'}
                    ]
                    ,
                    state: [
                        {required: true, message:'状态不能为空',trigger:'blur'}
                    ]
                }
            }
        },
        methods: {
            reset(form){
                this.$refs[form].resetFields();
            },
            addDept(){
                this.modal2 = true;
            },
            add(){
                this.$refs['addForm'].validate((valid)=>{
                    if(valid)
                    {
                        const dept = this.addForm;
                        fetch({
                            url: '/system/dept',
                            method: 'post',
                            data: dept
                        }).then((result) => {
                            this.gopage(this.pageNo);
                            this.$refs['addForm'].resetFields();
                            this.$Message.success('Success!');
                            this.modal2 = false;
                        });
                    }
                    else
                    {
                        this.$Message.error("表单验证失败");
                            setTimeout(()=>{
                                this.loading=false;
                                this.$nextTick(()=>{
                                    this.loading=true;
                                });
                            },1000);
                    }
                })
            },
            change(e){
                if (e.length == 1) {
                    this.updateForm = e[0];
                }
                this.setGroupId(e);
            },
            setGroupId(e)
            {
                this.groupId = [];
                this.count = e.length;
                for (var i = 0; i < e.length; i++) {
                    this.groupId.push(e[i].id);
                }
            },
            edit () {
                if (this.count != 1) {
                    this.modal1 = false;
                    this.$Message.warning('请至少并只能选择一项');
                }
                else {
                    this.modal1 = true;
                    this.updateForm.state = this.updateForm.state+"";
                }
            },
            remove () {
                if (this.groupId != null && this.groupId != "") {
                    fetch({
                        url: '/system/dept',
                        method: 'delete',
                        data: this.groupId
                    }).then((result) => {
                        if (result.data == '1') {
                            this.$Message.success('Success!');
                            this.gopage(this.pageNo);
                        }
                    });
                } else {
                    this.$Message.warning('请至少选择一项');
                }

            },
            update () {

                this.$refs['updateForm'].validate((valid)=>{
                    if(valid)
                    {
                        fetch({
                            url: '/system/dept',
                            method: 'put',
                            data: this.updateForm
                        }).then((result) => {
                            this.modal1 = false,
                                    this.$Message.success('Success!');
                            //刷新页面数据
                            this.gopage(this.pageNo);
                        });
                    }
                    else
                    {
                        this.$Message.error("表单验证失败");
                        setTimeout(()=>{
                            this.loading=false;
                            this.$nextTick(()=>{
                                this.loading=true;
                            });
                        },1000);
                    }
                })

            },
            handleReset (form) {
                this.$refs[form].resetFields();
            },
            gopage(){
                const pageNo = this.pageNo;
                const pageSize = this.pageSize;
                const deptNo = this.deptNo;
                fetch({
                    url: '/system/dept',
                    method: 'get',
                    params: {pageNo, pageSize,deptNo}
                }).then((result) => {
                    this.data1 = result.data.list;
                    this.pageNo = pageNo;
                    this.pageSize = pageSize;
                    this.totalCount = result.data.totalCount;
                });
            },
            submit(){
                if (this.groupId != null && this.groupId != "") {
                    fetch({
                        url: '/system/dept/submit',
                        method: 'put',
                        data: this.groupId
                    }).then((result) => {
                        if (result.data == '1') {
                            this.$Message.success('Success!');
                            this.gopage();
                        }
                    });
                } else {
                    this.$Message.warning('请至少选择一项');
                }
            },
            cancel () {
                this.$Message.info('点击了取消');
            }
        },
        mounted: function () {
            this.gopage()
        }
    }


</script>
