<template>
    <div class="animated fadeIn">
        <div>
            <ul>
                <li>
                    <Button type="primary" icon="plus-round" @click="addContractProduct()">新建</Button>
                    <Button type="success" icon="wrench" @click="edit()">修改</Button>
                    <Button type="error" icon="trash-a" @click="remove()">删除</Button>
                    <Button type="info" icon="arrow-left-c" @click="()=>{this.$router.go(-1)}">返回</Button>
                </li>
                <li>
                    <div style="padding: 10px 0;">
                        <Table :columns="columns1" :data="data1" @on-selection-change="s=>{change(s)}"></Table>
                        <!--<Table border :columns="columns1" :data="data1" :height="400" @on-selection-change="s=>{change(s)}" @on-row-dblclick="s=>{dblclick(s)}"></Table>-->
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
                title="编辑购销合同货物-附件"
                width="60%">
            <Form ref="updateForm" :model="updateForm" :rules="ruleCustom" :label-width="80">
                <Row>
                    <Col span="11">
                    <FormItem label="生产厂家" prop="factory.id">
                        <Select v-model="updateForm.factory.id" filterable @on-change="o=>changeFactory(this.updateFrom,o)" label-in-value=true>
                            <Option v-for="item in factoryList" :value="item.id" :key="item.id" >{{ item.factoryName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="货号" prop="productNo">
                        <Input type="text" v-model="updateForm.productNo"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="数量" prop="cnumber">
                        <Input type="text" v-model="updateForm.cnumber"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="包装单位" prop="packingUnit">
                        <Radio-group v-model="updateForm.packingUnit" type="button">
                            <Radio label="PCS" >只</Radio>
                            <Radio label="SETS">套</Radio>
                        </Radio-group>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="单价" prop="price">
                        <Input type="text" v-model="updateForm.price"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="排序号" prop="orderNo">
                        <Input type="text" v-model="updateForm.orderNo"/>
                    </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="货物描述" prop="productDesc">
                        <Input type="textarea" v-model="updateForm.productDesc"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="要求" prop="productRequest">
                        <Input type="textarea" v-model="updateForm.productRequest"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="24">
                    <FormItem>
                        <Button type="primary" @click="update">保存</Button>
                        <Button type="ghost" @click="reset('updateForm')">清空</Button>
                    </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <Modal
                v-model="modal2"
                title="添加购销合同货物-附件"
                width="60%">
            <Form ref="addForm" :model="addForm" :rules="ruleCustom" :label-width="80">
                <Row>
                    <Col span="11">
                    <FormItem label="生产厂家" prop="factory.id">
                        <Select v-model="addForm.factory.id" filterable @on-change="o=>changeFactory(this.addForm,o)" label-in-value="true">
                            <Option v-for="item in factoryList" :value="item.id" :key="item.id" >{{ item.factoryName }}</Option>
                        </Select>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="货号" prop="productNo">
                        <Input type="text" v-model="addForm.productNo"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="数量" prop="cnumber">
                        <Input type="text" v-model="addForm.cnumber"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="包装单位" prop="packingUnit">
                        <Radio-group v-model="addForm.packingUnit" type="button">
                            <Radio label="PCS" >只</Radio>
                            <Radio label="SETS">套</Radio>
                        </Radio-group>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="装率" prop="loadingRate">
                        <Input type="text" v-model="addForm.loadingRate"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="箱数" prop="boxNum">
                        <Input type="text" v-model="addForm.boxNum"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="11">
                    <FormItem label="单价" prop="price">
                        <Input type="text" v-model="addForm.price"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="排序号" prop="orderNo">
                        <Input type="text" v-model="addForm.orderNo"/>
                     </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="11">
                    <FormItem label="货物描述" prop="productDesc">
                        <Input type="textarea" v-model="addForm.productDesc"/>
                    </FormItem>
                    </Col>
                    <Col span="2" style="text-align: center"/>
                    <Col span="11">
                    <FormItem label="要求" prop="productRequest">
                        <Input type="textarea" v-model="addForm.productRequest"/>
                    </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="24">
                    <FormItem>
                        <Button type="primary" @click="add">保存</Button>
                        <Button type="ghost" @click="reset('addForm')">清空</Button>
                    </FormItem>
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
            const validateUser = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入购销合同货物-附件名'));
                }
            };
            const validateAddr = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入地址'));
                }
            };
            const validateid = (rule, value, callback) => {
                if (value === '') {
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
                contractProductId:null,
                count: 0,
                gourpId: null,
                tempIndex: 0,
                pageSize: 20,
                pageNo: 1,
                totalPage: 0,
                totalCount: 0,
                columns1: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '厂家',
                        key: 'factoryName'
                    },
                    {
                        title: '货号',
                        key: 'productNo'
                    },
                    {
                        title: '包装单位',
                        key: 'packingUnit'
                    },
                    {
                        title: '数量',
                        key: 'cnumber'
                    },
                    {
                        title: '单价',
                        key: 'price'
                    },
                    {
                        title: '总金额',
                        key: 'amount'
                    }
                ],
                self: this,
                data1: [],
                modal1: false,
                modal2: false,
                updateForm: {
                    factory: {
                        id:""
                    },
                    contractProduct:{
                        id:""
                    },
                    factoryName:"",
                    productNo:"",
                    productImage:"",
                    productDesc:"",
                    packingUnit:"",
                    cnumber:"",
                    outNumber:"",
                    finished:"",
                    productRequest:"",
                    price:"",
                    amount:"",
                    orderNo:""
                },
                addForm: {
                    factory: {
                        id:""
                    },
                    contractProduct:{
                        id:""
                    },
                     factoryName:"",
                     productNo:"",
                     productImage:"",
                     productDesc:"",
                     packingUnit:"",
                     cnumber:"",
                     outNumber:"",
                     finished:"",
                     productRequest:"",
                     price:"",
                     amount:"",
                     orderNo:""
                },
                ruleCustom: {
                    extCproductName: [
                        {validator: validateUser, trigger: 'blur'}
                    ],
                    id: [
                        {validator: validateid, trigger: 'blur'}
                    ]
                }
            }
        },
        methods: {
            changeFactory(form,option){
                form.factoryName = option.label;
            },
            reset(form){
                this.$refs[form].resetFields();
            },
            addContractProduct(){
                this.modal2 = true;
            },
            add(){
                this.addForm.contractProduct.id = this.contractProductId;
                const extCproduct = this.addForm;

                fetch({
                    url: '/cargo/extCproduct',
                    method: 'post',
                    data: extCproduct
                }).then((result) => {
//                    this.data1.push(result.data);
                    this.gopage(this.pageNo);
                    this.$refs['addForm'].resetFields();
                    this.$Message.success('Success!');
                    this.modal2 = false;
                });
            },
            change(e){
                if (e.length == 1) {
                    this.updateForm = e[0];
                    //空值处理一下
                    if(this.updateForm.factory==null)
                    {
                        this.updateForm.factory = {id:null};
                    }
                    //将importNum 转成字符串
//                    this.updateForm.importNum = this.updateForm.importNum + "";
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
                }
            },
            remove () {
                if (this.groupId != null && this.groupId != "") {
                    fetch({
                        url: '/cargo/extCproduct',
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
                fetch({
                    url: '/cargo/extCproduct',
                    method: 'put',
                    data: this.updateForm
                }).then((result) => {
                    this.modal1 = false,
                    this.$Message.success('Success!');
                    //刷新页面数据
                    this.gopage(this.pageNo);
                });
            },
            handleReset (form) {
                this.$refs[form].resetFields();
            },
            gopage(pageNo){
                const pageSize = this.pageSize;
                const contractProductId = this.contractProductId;
                fetch({
                    url: '/cargo/extCproduct',
                    method: 'get',
                    params: {pageNo, pageSize,contractProductId}
                }).then((result) => {
                    this.data1 = result.data.list;
                    this.pageNo = pageNo;
                    this.pageSize = pageSize;
                    this.totalCount = result.data.totalCount;
                });
            }
        },
        mounted: function () {
            //接受url参数
            this.contractProductId = this.$route.params.id;
            this.gopage(this.pageNo);

            //初始化部门列表
            fetch({
                url:'/cargo/factory/getAll',
                method:'get',
                params:{cType:"附件"}
            }).then((result)=>{
                this.factoryList = result.data;
            })
        }
    }


</script>