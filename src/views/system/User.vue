<template>


    <div class="animated fadeIn">
        <!--<Icon type="android-add"></Icon>-->
        <div id="container">
            <Icon type="android-add-circle"></Icon>
            <i-button type="error" @click="addUser">添加用户</i-button>
        </div>
        <br>
        <Table :columns="columns1" :data="data1"></Table>
        <Page :total="totalCount" :page-size="pageSize" :current="pageNo" @on-change="gopage" align="center"></Page>
        <Modal
                v-model="modal1"
                title="编辑用户"
                @on-ok="update"
                @on-cancel="cancel" width="60%">
            <Form ref="updateForm" :model="updateForm" :rules="ruleCustom" :label-width="80">
                <Row>
                    <Col span="11">
                    <FormItem label="所在部门" prop="dept.id">
                        <Select v-model="updateForm.dept.id" filterable>
                            <Option v-for="item in deptList" :value="item.id" :key="item.id">{{ item.deptName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="直属领导" prop="userInfo.manager.id">
                        <Select v-model="updateForm.userInfo.manager.id">
                            <Option v-for="item in userList" :value="item.id" :disabled="item.id==updateForm.id" :key="item.id">{{ item.userName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                </Row>


                <Row>
                    <Col span="11">
                    <FormItem label="登录名" prop="userName"  >
                        <Input type="text" readonly v-model="updateForm.userName"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="姓名" prop="userInfo.name"  >
                        <Input type="text" v-model="updateForm.userInfo.name"></Input>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="入职日期" prop="userInfo.joinDate"  >
                        <DatePicker v-model="updateForm.userInfo.joinDate" format="yyyy-MM-dd" type="date" placeholder="Select date" style="width: 200px"></DatePicker>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="薪水" prop="userInfo.salary"  >
                        <Input type="text" v-model="updateForm.userInfo.salary"></Input>
                    </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="等级" prop="userInfo.degree"  >
                        <Select v-model="updateForm.userInfo.degree" filterable>
                            <Option  v-for="item in degreeList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="性别" prop="userInfo.gender"  >
                        <RadioGroup v-model="updateForm.userInfo.gender" type="button">
                            <Radio  label="1">男</Radio>
                            <Radio  label="0">女</Radio>
                        </RadioGroup>
                    </FormItem>
                    </Col>
                    <Col span="11">
                    <FormItem label="状态" prop="state"  >
                        <Radio-group v-model="updateForm.state" type="button">
                            <Radio label="1" >启用</Radio>
                            <Radio label="0">停用</Radio>
                        </Radio-group>
                    </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="岗位" prop="userInfo.station"  >
                        <Input type="text" v-model="updateForm.userInfo.station"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="电话" prop="userInfo.telephone"  >
                        <Input type="text" v-model="updateForm.userInfo.telephone"></Input>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="邮箱" prop="userInfo.email"  >
                        <Input type="text" v-model="updateForm.userInfo.email"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="出生年月" prop="userInfo.birthday"  >
                        <DatePicker type="date" v-model="updateForm.userInfo.birthday" format="yyyy-MM-dd" placeholder="Select date" style="width: 200px"></DatePicker>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">

                    <FormItem label="排序号" prop="userInfo.orderNo"  >
                        <Input type="text" v-model="updateForm.userInfo.orderNo"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="说明" prop="userInfo.remark"  >
                        <Input type="textarea" v-model="updateForm.userInfo.remark"></Input>
                    </FormItem>
                    </Col>
                </Row>

            </Form>
        </Modal>
        <Modal
                v-model="modal2"
                title="添加用户"
                @on-ok="add"
                @on-cancel="cancel" width="60%">
            <Form ref="addForm" :model="addForm" :rules="ruleCustom" :label-width="80">
                <Row>
                    <Col span="11">
                    <FormItem label="所在部门" prop="dept.id">
                        <Select v-model="addForm.dept.id" filterable>
                            <Option v-for="item in deptList" :value="item.id" :key="item.id">{{ item.deptName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="直属领导" prop="userInfo.manager.id">
                        <Select v-model="addForm.userInfo.manager.id" filterable>
                            <Option v-for="item in userList" :value="item.id" :key="item.id">{{ item.userName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                </Row>


                <Row>
                    <Col span="11">
                    <FormItem label="登录名" prop="userName"  >
                        <Input type="text" v-model="addForm.userName"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="姓名" prop="userInfo.name"  >
                        <Input type="text" v-model="addForm.userInfo.name"></Input>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="入职日期" prop="userInfo.joinDate"  >
                        <DatePicker v-model="addForm.userInfo.joinDate" type="date" placeholder="Select date" style="width: 200px"></DatePicker>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="薪水" prop="userInfo.salary"  >
                        <Input type="text" v-model="addForm.userInfo.salary"></Input>
                    </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="等级" prop="userInfo.degree"  >
                        <Select v-model="addForm.userInfo.degree" filterable>
                            <Option  v-for="item in degreeList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="性别" prop="userInfo.gender"  >
                        <RadioGroup v-model="addForm.userInfo.gender" type="button">
                            <Radio  label="1">男</Radio>
                            <Radio  label="0">女</Radio>
                        </RadioGroup>
                    </FormItem>
                    </Col>
                    <Col span="11">
                    <FormItem label="状态" prop="state"  >
                        <Radio-group v-model="addForm.state" type="button">
                            <Radio label="1" >启用</Radio>
                            <Radio label="0">停用</Radio>
                        </Radio-group>
                    </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="岗位" prop="userInfo.station"  >
                        <Input type="text" v-model="addForm.userInfo.station"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="电话" prop="userInfo.telephone"  >
                        <Input type="text" v-model="addForm.userInfo.telephone"></Input>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="邮箱" prop="userInfo.email"  >
                        <Input type="text" v-model="addForm.userInfo.email"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="出生年月" prop="userInfo.birthday"  >
                        <DatePicker type="date" v-model="addForm.userInfo.birthday" placeholder="Select date" style="width: 200px"></DatePicker>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">

                    <FormItem label="排序号" prop="userInfo.orderNo"  >
                        <Input type="text" v-model="addForm.userInfo.orderNo"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="说明" prop="userInfo.remark"  >
                        <Input type="textarea" v-model="addForm.userInfo.remark"></Input>
                    </FormItem>
                    </Col>
                </Row>

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
                    callback(new Error('请输入用户名'));
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
                        title: '用户名',
                        key: 'userName'
                    },
                    {
                        title: '状态',
                        ellipsis:'true',
                        key:'state',
                        filters: [
                            {
                                label: '启用',
                                value: 1
                            },
                            {
                                label: '禁用',
                                value: 0
                            }
                        ],
                        filterMultiple: false,
                        filterMethod (value, row) {
                            if (value === 1) {
                                return row.state===1;
                            } else if (value === 0) {
                                return row.state===0;
                            }
                        },
                        render: (h, params) => {
                            const task_status=parseInt(params.row.state);

                            if(task_status===1)
                                return h('div', [
                                    h('Tag', {
                                        props: {
                                            type: 'dot',
                                            color: "green"
                                        }
                                    }, "启用"),
                                ]);

                            else if(task_status===0)
                                return h('div', [

                                    h('Tag', {
                                        props: {
                                            type: 'dot',
                                            color: "red"
                                        }
                                    }, "禁用"),
                                ]);
                        }
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
                addForm: {
                    "dept":{
                        id:""
                    },
                    "userName": "",
                    "state": "",
                    "userInfo":{
                        "name": "",
                        "manager":{
                            id:""
                        },
                        "joinDate": "",
                        "salary": "",
                        "degree": "",
                        "gender": "",
                        "station": "",
                        "telephone": "",
                        "email": "",
                        "birthday": "",
                        "orderNo": "",
                        "remark": ""
                    }
                },
                updateForm: {
                    "dept":{
                        id:""
                    },
                    "userName": "",
                    "state": "",
                    "userInfo":{
                        "name": "",
                        "manager":{
                            id:""
                        },
                        "joinDate": "",
                        "salary": "",
                        "degree": "",
                        "gender": "",
                        "station": "",
                        "telephone": "",
                        "email": "",
                        "birthday": "",
                        "orderNo": "",
                        "remark": ""
                    }
                },
                ruleCustom: {
//                    userName: [
//                        { validator: validateUser, trigger: 'blur' }
//                    ],
//                    id: [
//                        { validator: validateid, trigger: 'blur' }
//                    ]
                },
                deptList: {},
                userList:{},
                degreeList:[
                    {
                        value: '0',
                        label: '超级管理员'
                    },
                    {
                        value: '1',
                        label: '跨部门跨人员'
                    },
                    {
                        value: '2',
                        label: '管理所有下属部门和人员'
                    },
                    {
                        value: '3',
                        label: '管理本部门'
                    },
                    {
                        value: '4',
                        label: '普通员工'
                    }
                ],
                model11: '',
                model12: []
            }
        },
        methods: {
            addUser(){
                this.modal2=true;
            },
            add(){
                const addForm = this.addForm;

                fetch({
                    url: '/system/user',
                    method: 'post',
                    data:addForm
                }).then((result) => {
                    this.data1.unshift(result.data);
                    this.$Message.success('Success!');
                });
            },
            show (index) {
                this.$Modal.info({
                    title: '用户信息',
                    content: `姓名：${this.data1[index].userName}`
                })
            },
            edit (index) {
                this.modal1=true;
                this.tempIndex=index;
                var editUser =this.data1[index];

                //手动赋值的原因：因为data1中的字段太多，上传到服务器 json转对象报错
                if(editUser.dept != undefined)
                {
                    this.updateForm.dept.id= editUser.deptId;
                }
                if(editUser.managerId!=undefined)
                {
                    this.updateForm.userInfo.manager.id= editUser.managerId;
                }
                if(editUser.userInfo != undefined)
                {


                    this.updateForm.userName=editUser.userName;
                    this.updateForm.id=editUser.id;
                    this.updateForm.state=editUser.state+"";
                    this.updateForm.userInfo.name=editUser.userInfo.name;
                    this.updateForm.userInfo.joinDate=editUser.userInfo.joinDate;
                    this.updateForm.userInfo.salary=editUser.userInfo.salary;
                    this.updateForm.userInfo.degree=editUser.userInfo.degree+"";
                    this.updateForm.userInfo.gender=editUser.userInfo.gender+"";
                    this.updateForm.userInfo.station=editUser.userInfo.station;
                    this.updateForm.userInfo.telephone=editUser.userInfo.telephone;
                    this.updateForm.userInfo.email=editUser.userInfo.email;
                    this.updateForm.userInfo.birthday=editUser.userInfo.birthday;
                    this.updateForm.userInfo.orderNo=editUser.userInfo.orderNo;
                    this.updateForm.userInfo.remark=editUser.userInfo.remark;
                }
            },
            remove (index) {
                const id = this.data1[index].id;
                fetch({
                    url: '/system/user',
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
            update () {
                const updateForm = this.updateForm;
                fetch({
                    url: '/system/user',
                    method: 'put',
                    data:updateForm
                }).then((result) => {
                    this.data1.unshift(result.data);
                    this.$Message.success('Success!');
                });

            },
            handleReset (userName) {
                this.$refs[userName].resetFields();
            },
            gopage(pageNo){
                const pageSize = this.pageSize;
                fetch({
                    url: '/system/user',
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
            //加载所有部门
            fetch({
                url: '/system/dept/getAll',
                method: 'get',

            }).then((result) => {
                this.deptList=result.data;
            });

            //加载所有员工（直属领导）
            fetch({
                url: '/system/user/getAll',
                method: 'get'
            }).then((result) => {
                this.userList=result.data;
            });

        }
    }
</script>