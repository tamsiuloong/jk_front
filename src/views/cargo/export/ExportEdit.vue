<template>


    <div class="animated fadeIn">
        <!--<div>-->
        <!--<Row style="margin-bottom: 25px;">-->
        <!--<Col span="8">登录名：-->
        <!--<Input v-model="loginName" placeholder="请输入..." style="width:200px"></Input>-->
        <!--</Col>-->
        <!--<Col span="8"><Button type="primary" shape="circle" icon="ios-search" @click="search()">搜索</Button></Col>-->
        <!--</Row>-->
        <!--</div>-->
        <div>
            <ul>
                <!--<li>-->
                    <!--<Button type="primary" icon="plus-round" @click="addContract()">新建</Button>-->
                    <!--<Button type="success" icon="wrench" @click="edit()">修改</Button>-->
                    <!--<Button type="error" icon="trash-a" @click="remove()">删除</Button>-->
                    <!--<Button type="warning"  @click="submit()">提交</Button>-->
                    <!--<Button type="info"  @click="cancel()">取消</Button>-->
                <!--</li>-->

                <li>
                    <div style="padding: 10px 0;">
                    <Form ref="updateForm" :model="updateForm"  :label-width="80">
                        <Row>
                            <Col span="11">
                            <FormItem label="信用证号" prop="lcno">
                                <Input type="text" v-model="updateForm.lcno" @on-change="changeValue"/>
                            </FormItem>
                            </Col>
                            <Col span="2" style="text-align: center"/>
                            <Col span="11">
                            <FormItem label="收货人及地址" prop="consignee">
                                <Input type="text" v-model="updateForm.consignee"/>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <Col span="11">
                            <FormItem label="唛头" prop="marks">
                                <Input type="text" v-model="updateForm.marks"/>
                            </FormItem>
                            </Col>
                            <Col span="2" style="text-align: center"/>
                            <Col span="11">
                            <FormItem label="装运港" prop="shipmentPort">
                                <Input type="text" v-model="updateForm.shipmentPort"/>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <Col span="11">
                            <FormItem label="目的港" prop="destinationPort">
                                <Input type="text" v-model="updateForm.destinationPort"/>
                            </FormItem>
                            </Col>
                            <Col span="2" style="text-align: center"/>
                            <Col span="11">
                            <FormItem label="运输方式" prop="transportMode">
                                <Input type="text" v-model="updateForm.transportMode"/>
                            </FormItem>
                            </Col>
                        </Row>
                        <Row>
                            <Col span="11">
                            <FormItem label="价格条件" prop="priceCondition">
                                <Input type="text" v-model="updateForm.priceCondition"/>
                            </FormItem>
                            </Col>
                            <Col span="2" style="text-align: center"/>
                            <Col span="11">
                            <FormItem label="备注" prop="remark">
                                <Input type="textarea" v-model="updateForm.remark"/>
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
                    </div>
                </li>
                <li>
                    <div style="padding: 10px 0;">
                        <Table  border :columns="columns1" :data="data1" @on-selection-change="s=>{change(s)}"></Table>
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

    </div>
</template>

<script type="text/ecmascript-6">
    import fetch from 'utils/fetch';
    import {dateFormat} from 'utils/date';

    export default {
        data() {

            return {
                exportId:"",
                count: 0,
                gourpId: null,
                pageSize: 20,
                pageNo: 1,
                totalPage: 0,
                totalCount: 0,
                columns1: [
                    /*{
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '保运号',
                        key: 'id'
                    },*/
                    {
                        title: '货号',
                        key: 'productNo'
                    },
                    {
                        title: '数量',
                        key: 'cnumber',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.cnumber,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.cnumber=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '毛重',
                        key: 'grossWeight',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.grossWeight,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.grossWeight=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '净重',
                        key: 'netWeight',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.netWeight,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.netWeight=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '长',
                        key: 'sizeLength',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.sizeLength,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.sizeLength=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '宽',
                        key: 'sizeWidth',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.sizeWidth,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.sizeWidth=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '高',
                        key: 'sizeHeight',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.sizeHeight,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.sizeHeight=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '出口单价',
                        key: 'exPrice',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.exPrice,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.exPrice=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    },
                    {
                        title: '含税',
                        key: 'tax',
                        render:  (h, params) => {
                            return h('div',[
                                h('Input',{
                                    props:{
                                        'type':"text",
                                        'value':params.row.tax,
                                        'number':true
                                    },
                                    on:{
                                        'on-blur':(e)=>{
                                            params.row.tax=e.target.value;
                                            fetch({
                                                url: '/cargo/exportProduct',
                                                method: 'put',
                                                data: params.row
                                            }).then((result) => {
                                                this.$Message.info("同步成功!")
                                            });
                                        }
                                    }
                                })
                            ])
                        }
                    }
                ],
                self: this,
                data1: [],
                modal1: false,
                modal2: false,
                updateForm: {
                    lcno: '',
                    inputDate: '',
                    contractIds: '',
                    customerContract: '',
                    consignee: '',
                    marks: '',
                    shipmentPort: '',
                    destinationPort: '',
                    transportMode: '',
                    priceCondition: '',
                    remark: '',
                    boxNums: '',
                    state: ''
                }
            }
        },
        methods: {
            reset(form){
                this.$refs[form].resetFields();
            },
            changeValue(e){
                alert(e);
            },
            change(e){
                if (e.length == 1) {
                    this.updateForm = e[0];
                    //将importNum 转成字符串
                    this.updateForm.importNum = this.updateForm.importNum + "";
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
            update () {
                fetch({
                    url: '/cargo/export',
                    method: 'put',
                    data: this.updateForm
                }).then((result) => {
                    this.$Message.info("修改出口保运成功!")
                });
            },
            handleReset (form) {
                this.$refs[form].resetFields();
            },
            gopage(pageNo){
                const pageSize = this.pageSize;
                fetch({
                    url: '/cargo/exportProduct/'+this.$route.params.id,
                    method: 'get',
                    params: {pageNo, pageSize}
                }).then((result) => {
                    this.data1 = result.data.list;
                    this.pageNo = pageNo;
                    this.pageSize = pageSize;
                    this.totalCount = result.data.totalCount;
                });
            }
        },
        mounted: function () {
            //加载出口保运信息
            this.exportId = this.$route.params.id;
            fetch({
                url: '/cargo/export/'+this.exportId,
                method: 'get'
            }).then((result) => {
                this.updateForm = result.data;
            });

            //加载出口保运货物信息
            this.gopage(this.pageNo,this.exportId);

        }
    }


</script>