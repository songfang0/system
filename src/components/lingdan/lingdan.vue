<template>
    <el-card class="box-card">
        <div slot="header" class="clearfix">
            <el-breadcrumb separator-class="el-icon-arrow-right">
                <el-breadcrumb-item :to="{ path: '/' }">发货</el-breadcrumb-item>
                <el-breadcrumb-item>零担</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="text item">
            <el-form :inline="true" :model="form" class="demo-form-inline" label-width="150px">
                <el-form-item label="出发地">
                    <el-cascader
                        :options="options"
                        v-model="selectedOptions1"
                        :props = 'defaultProp'
                        @change="handleChange($event)">
                    </el-cascader>
                </el-form-item>
                <el-form-item label="目的地">
                    <el-cascader
                        :options="options"
                        :props = 'defaultProp'
                        v-model="selectedOptions2"
                        @change="handleChange">
                    </el-cascader>
                </el-form-item>
                <div>
                    <router-link to='/detail'>
                        <el-button class='detailBtn' type="primary" plain>详细体积重量</el-button>
                    </router-link>
                </div>
                <el-form-item label="体积">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="重量">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="类型">
                    <el-select v-model="goodtype" placeholder="请选择">
                        <el-option
                            v-for="item in GoodsType"
                            :key="item.id"
                            :label="item.typeName"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="包装形式">
                    <el-select v-model="PackingTypeValue" placeholder="请选择">
                        <el-option
                            v-for="item in PackingType"
                            :key="item.id"
                            :label="item.typeName"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="装货时间">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="到货时间">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="收货人">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="电话">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="收获详细地址">
                    <el-input v-model="form.user" placeholder="审批人"></el-input>
                </el-form-item>
                <el-form-item label="车辆类型">
                    <el-select v-model="CarTypeValue" placeholder="请选择">
                        <el-option
                            v-for="item in CarType"
                            :key="item.id"
                            :label="item.typeName"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item class='file' label="上传商品照片">
                    <el-upload
                        action="https://jsonplaceholder.typicode.com/posts/"
                        list-type="picture-card"
                        :on-remove="handleRemove">
                        <i class="el-icon-plus"></i>
                    </el-upload>
                    <el-dialog :visible.sync="dialogVisibleImg">
                        <img width="100%" :src="dialogImageUrl" alt="">
                    </el-dialog>
                </el-form-item>
                <el-form-item label="备注" class='file'>
                    <el-input type="textarea" v-model="form.desc"></el-input>
                </el-form-item>
            </el-form>
            <el-button class='btn' type="primary" plain　@click='submitData()'>确认</el-button>
        </div>
        <el-dialog
            title="提交成功"
            :visible.sync="dialogVisibleItem"
            width="30%">
            <span>系统正在给你匹配最优秀的承运商</span>
            <span slot="footer" class="dialog-footer">
                <el-button type="primary" @click="submitOrder()">确 定</el-button>
            </span>
        </el-dialog>
    </el-card>
</template>

<script>
export default {
    data(){
        return {
            form: {},
            // 出发地级联选择器数组
            options: [],
            // 级联选择器选中的id
            selectedOptions1: [],
            // 目的地级联选择器选中的id
            selectedOptions2: [],
            defaultProp: {
                label: 'regionName',
                value: 'areaId',
                children: 'children'
            },
            // 当前选中的级联选择器的region_code
            currentRegion_code: '',
            currentPage: 1,
            pageSize: 0,
            dialogImageUrl: '',
            // 图片上传
            dialogVisibleImg: false,
            // 获取类型
            GoodsType: [],
            // 当前选中的货物的值
            goodtype: '',
            // 包装类型
            PackingType: [],
            PackingTypeValue: '',
            // 车辆类型查询
            CarType: [],
            CarTypeValue: '',
            // 点击确认对话框
            dialogVisibleItem: false
        }
    },
    created(){
        // 地域三级联动查询
        this.getSysGetArea();
        // 获取货物类型
        this.getSysGoodsType();
        // 获取包装类型
        this.getSysPackingType();
        // 车辆类型查询
        this.getSysCarType();
    },
    methods: {
        // 点击对话框的成功提示，跳转到支付详情页
        submitOrder(){
            this.dialogVisibleItem = false;
            this.$router.push({
                name: 'lingdanOrder'
            });
        },
        // 点击提交弹出对话框
        submitData(){
            // 提交数据成功之后打开对话框提示
            this.dialogVisibleItem = true;
        },
        // 图片移除时触发
        handleRemove(){

        },
        // 级联选择器改变时触发
        async handleChange(e){
           console.log(e);
        },
        // 地域三级联动查询
        async getSysGetArea(){
            const res = await this.$http.get(`/common/api/sysGetArea?pid=000000`);
            console.log(res);
            this.options = res.data.data;
        },
        // 货物类型查询
        async getSysGoodsType(){
            const res = await this.$http.get(`/common/api/sysGoodsType?currentPage=${this.currentPage}&pageSize=${this.pageSize}`);
            // console.log(res);
            this.GoodsType = res.data.data;
        },
        // 包装类型查询
        async getSysPackingType(){
            const res = await this.$http.get(`/common/api/sysPackingType?currentPage=${this.currentPage}&pageSize=0`);
            // console.log(res);
            this.PackingType = res.data.data;
        },
        // 车辆类型查询
        async getSysCarType(){
            const res = await this.$http.get(`/common/api/sysCarType?currentPage=${this.currentPage}&pageSize=${this.pageSize}`);
            console.log(res);
            this.CarType = res.data.data;
        }
    }
}
</script>

<style scoped>
    .box-card {
        height: 100%;
    }
    .el-form--inline .file {
        display: block;
    }
    .el-textarea__inner {
        width: 300px;
    }
    .text {
        height: 450px;
        overflow: auto;
    }
    .btn {
        margin-left: 150px;
    }
    .el-form-item {
        width: 400px;
    }
    .el-form-item__content {
        width: 217px;
    }
    .file {
        width: 100%;
    }
    .el-textarea__inner {
        width: 400px;
    }
    .detailBtn {
        margin: 0px 0 15px 150px;
    }
</style>
