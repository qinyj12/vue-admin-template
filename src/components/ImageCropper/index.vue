<template>
    <div>
        <!-- element 上传图片按钮 -->
        <el-upload
            class="upload-demo"
            action=""
            drag
            :auto-upload="false"
            :show-file-list="false"
            :on-change="changeUpload"
        >
            <img v-if="imageCut" :src="imageCut" class="image-cut"> <!--这个显示剪裁好以后的图片-->
            <i class="el-icon-upload" />
            <div class="el-upload__text">点击上传</div>
            <div class="el-upload__tip">支持绝大多数图片格式，单张图片最大支持5MB</div>
        </el-upload>

        <el-dialog title="图片剪裁" :visible.sync="dialogVisible" append-to-body>
            <div class="cropper-content">
                <div class="cropper" style="text-align:center">
                    <vueCropper
                        ref="cropper"
                        :img="option.img"
                        :outputSize="option.size"
                        :outputType="option.outputType"
                        :info="true"
                        :full="option.full"
                        :canMove="option.canMove"
                        :canMoveBox="option.canMoveBox"
                        :original="option.original"
                        :autoCrop="option.autoCrop"
                        :autoCropWidth="option.autoCropWidth"
                        :autoCropHeight="option.autoCropHeight"
                        :fixedBox="option.fixedBox"
                    >
                    </vueCropper>
                </div>
            </div>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" :loading="loading" @click="finish">确认</el-button>
            </div>
        </el-dialog>
    </div>
</template>
<script>
export default {
    data() {
        return {
            dialogVisible: false,
            // 裁剪组件的基础配置option
            option: {
                img: '', // 裁剪图片的地址
                info: true, // 裁剪框的大小信息
                size: 0.8, // 裁剪生成图片的质量
                outputType: 'jpeg', // 裁剪生成图片的格式
                canScale: false, // 图片是否允许滚轮缩放
                autoCrop: true, // 是否默认生成截图框
                autoCropWidth: 360, // 默认生成截图框宽度
                autoCropHeight: 180, // 默认生成截图框高度
                fixedBox: true, // 固定截图框大小 不允许改变
                fixed: true, // 是否开启截图框宽高固定比例
                fixedNumber: [7, 5], // 截图框的宽高比例
                full: true, // 是否输出原图比例的截图
                canMoveBox: false, // 截图框能否拖动
                original: false, // 上传图片按照原始比例渲染
                centerBox: false, // 截图框是否被限制在图片里面
                infoTrue: true // true 为展示真实输出图片宽高 false 展示看到的截图框宽高
            },
            picsList: [], // 页面显示的数组
            // 防止重复提交
            loading: false,
            imageCut: '' // 已经剪裁好的图片
        }
    },
    methods: {
        // 上传按钮   限制图片大小
        changeUpload(file, fileList) {
            const isLt5M = file.size / 1024 / 1024 < 5
            if (!isLt5M) {
                this.$message.error('上传文件大小不能超过 5MB!')
                return false
            }
            this.fileinfo = file
            // 获取的是blob对象
            const url = URL.createObjectURL(file.raw)

            // 上传成功后将图片地址赋值给裁剪框显示图片
            this.$nextTick(() => {
                this.option.img = url
                this.dialogVisible = true
                console.log(this.option.img)
            })
        },
        // 点击裁剪，这一步是可以拿到处理后的地址
        finish() {
            this.$refs.cropper.getCropBlob((data) => {
                console.log(data)
                // var fileName = "goods" + this.fileinfo.uid
                this.dialogVisible = false
                this.imageCut = URL.createObjectURL(data) // 把剪裁好的 图片放在上传框里
                this.uploadHeadUrl_c(data)
            })
        },
        // 上传到服务器
        uploadHeadUrl_c(file) {
            const formData = new FormData()
            formData.append('moduleName', 'headUrl')
            formData.append('uploadFile', file)
            console.log(formData)
            // 此处加入ajax，把formdata传到后端
        }
    }
}
</script>
<style>
.el-upload-dragger {
    width: 360px;
    height: 180px;
}

.image-cut {
    width: 360px;
    height: 180px;
}

.cropper {
    width: auto;
    height: 300px;
}
</style>
