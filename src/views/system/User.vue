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
                @on-cancel="cancel" width="50%">
            <Form ref="updateForm" :model="updateForm" :rules="ruleCustom" :label-width="80">
                <FormItem label="所在部门" prop="dept.id">
                    <Select v-model="updateForm.deptId" filterable>
                        <Option v-for="item in cityList" :value="item.id" :key="item.id">{{ item.deptName }}</Option>
                    </Select>
                </FormItem>
                <FormItem label="登录名" prop="userName"  >
                    <Input type="text" v-model="updateForm.userName"></Input>
                </FormItem>
                <FormItem label="姓名" prop="userInfoName"  >
                    <Input type="text" v-model="updateForm.userInfoName"></Input>
                </FormItem>
                <FormItem label="直属领导" prop="userInfoManagerId">
                    <Select v-model="updateForm.userInfoManagerId" filterable>
                        <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                    </Select>
                </FormItem>

                <!--<FormItem>-->
                <!--<Button type="primary" @click="update('updateForm')">保存</Button>-->
                <!--<Button type="ghost" @click="handleReset('updateForm')" style="margin-left: 8px">Reset</Button>-->
                <!--</FormItem>-->
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
                    <FormItem label="所在部门" prop="deptId">
                        <Select v-model="addForm.deptId" filterable>
                            <Option v-for="item in deptList" :value="item.id" :key="item.id">{{ item.deptName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="直属领导" prop="managerId">
                        <Select v-model="addForm.managerId" filterable>
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
                    <FormItem label="姓名" prop="name"  >
                        <Input type="text" v-model="addForm.name"></Input>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="入职日期" prop="joinDate"  >
                        <DatePicker v-model="addForm.joinDate" type="date" placeholder="Select date" style="width: 200px"></DatePicker>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="薪水" prop="salary"  >
                        <Input type="text" v-model="addForm.salary"></Input>
                    </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="等级" prop="degree"  >
                        <Select v-model="addForm.degree" filterable>
                            <Option  v-for="item in degreeList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="性别" prop="gender"  >
                        <RadioGroup v-model="addForm.gender" type="button">
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
                    <FormItem label="岗位" prop="station"  >
                        <Input type="text" v-model="addForm.station"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="电话" prop="telephone"  >
                        <Input type="text" v-model="addForm.telephone"></Input>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="邮箱" prop="email"  >
                        <Input type="text" v-model="addForm.email"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="出生年月" prop="birthday"  >
                        <DatePicker type="date" v-model="addForm.birthday" placeholder="Select date" style="width: 200px"></DatePicker>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">

                    <FormItem label="排序号" prop="orderNo"  >
                        <Input type="text" v-model="addForm.orderNo"></Input>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"></Col>
                    <Col span="11">
                    <FormItem label="说明" prop="remark"  >
                        <Input type="textarea" v-model="addForm.remark"></Input>
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
                pageSize:2,
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
                                            this.show(params.index)
                                        }
                                    }
                                }, '查看'),
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
                    {
                        userName: '王小明',
                        id: 18,
                        state:1
                    }
                ],
                modal1: false,
                modal2: false,
                updateForm: {
                    deptId:'',
                    "userInfoName":'',
                    'userInfoManagerId':'',
                    userName: '',
                    id: ''
                },
                addForm: {
                    deptId:'',
                    userName: '',
                    state: 1,
                    name: '',
                    managerId: '',
                    joinDate: '',
                    salary: '',
                    degree: '',
                    gender: '',
                    station: '',
                    telephone: '',
                    email: '',
                    birthday: '',
                    orderNo: '',
                    remark:''
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
                cityList: [
                    {
                        value: 'New York',
                        label: 'New York'
                    },
                    {
                        value: 'London',
                        label: 'London'
                    },
                    {
                        value: 'Sydney',
                        label: 'Sydney'
                    },
                    {
                        value: 'Ottawa',
                        label: 'Ottawa'
                    },
                    {
                        value: 'Paris',
                        label: 'Paris'
                    },
                    {
                        value: 'Canberra',
                        label: 'Canberra'
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
                const user = {
                    "dept":{
                        id:addForm.deptId
                    },
                    "userName": addForm.userName,
                    "state": addForm.state,
                    "userInfo":{
                        "name": addForm.name,
                        "manager.id":addForm.managerId,
                        "joinDate": addForm.joinDate,
                        "salary": addForm.salary,
                        "degree": addForm.degree,
                        "gender": addForm.gender,
                        "station": addForm.station,
                        "telephone": addForm.telephone,
                        "email": addForm.email,
                        "birthday": addForm.birthday,
                        "orderNo": addForm.orderNo,
                        "remark": addForm.remark
                    }
                };


                fetch({
                    url: '/system/user',
                    method: 'post',
                    data:user
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
                this.updateForm.userName=this.data1[index].userName;
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
            update (userName) {
                this.data1[this.tempIndex].userName=this.updateForm.userName;
                this.data1[this.tempIndex].userInfo.manager.id=this.updateForm.userInfoManagerId;
                this.data1[this.tempIndex].dept.id=this.updateForm.deptId;
                
                const user = this.data1[this.tempIndex];

                fetch({
                    url: '/system/user',
                    method: 'put',
                    data:user
                }).then((result) => {
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
        mounted:function(){
            this.gopage(this.pageNo)

            fetch({
                url: '/system/dept/getAll',
                method: 'get'
            }).then((result) => {
                this.deptList=result.data;
            });
            fetch({
                url: '/system/user/getAll',
                method: 'get'
            }).then((result) => {
                this.userList=result.data;
            });

        }
    }
</script>