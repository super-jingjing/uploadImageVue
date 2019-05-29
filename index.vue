<template>
    <div class="dropzone">
        <input ref="uploadInput"
               :id="id"
               type="file"
               name="file"
               value=""
               accept="image/gif,image/jpeg,image/jpg,image/png"
               @change="selectImg($event)">
        <div :class="operationShow?'operation-div':'operation-div hide'">
            <img :src="defaultImg"
                 @click="showBigImg">
            <span v-if="!disable"
                  class="remove"
                  @click="remove">移除</span>
        </div>
        <div :class="operationShow?'txt hide':'txt'">
            +
            <span class="info">{{ info }}</span>
        </div>
        <el-dialog v-if="isSubDialog"
                   :visible.sync="dialogBigImgVisible"
                   top="20px"
                   append-to-body>
            <img :src="defaultImg"
                 width="100%"
                 alt="">
        </el-dialog>
        <el-dialog v-else
                   :visible.sync="dialogBigImgVisible"
                   top="20px">
            <img :src="defaultImg"
                 width="100%"
                 alt="">
        </el-dialog>
    </div>
</template>
<script>
export default {
    props: {
        url: {
            default: "",
            type: String
        },
        imgSrc: {
            default: "",
            type: String
        },
        autoUpload: {
            default: true,
            type: Boolean
        },
        info: {
            default: "",
            type: String
        },
        id: {
            default: "",
            type: String
        },
        disable: {
            default: false,
            type: Boolean
        },
        isSubDialog: {
            default: false,
            type: Boolean
        },
        needShowBigImg: {
            default: false,
            type: Boolean
        }
    },
    data()
    {
        return {
            dialogBigImgVisible: false, // 展示图片大图的dialog
            operationShow: false,
            uploadFile: "",
            defaultImg: process.env.BASE_API + "/service-admin/" + this.imgSrc
        };
    },
    watch: {
        imgSrc(newsVal)
        {
            this.init(newsVal);
        }
    },
    mounted()
    {
        this.init(this.imgSrc);
    },
    methods: {
        selectImg(e)
        {
            const imgFile = e.target.files[0];
            if (imgFile)
            {
                this.uploadFile = imgFile;
                let reader = new FileReader();
                reader.readAsDataURL(imgFile);
                reader.onload = (event) =>
                {
                    this.defaultImg = reader.result;
                    this.operationShow = true;
                    if (this.autoUpload)
                    {
                        this.upload();
                    }
                };
            }
        },
        beforeAvatarUploadIMG(file)
        {
            const isIMG = file.type === "image/png" || file.type === "image/jpg" || file.type === "image/jpeg";
            if (!isIMG)
            {
                this.$message.error("只能上传 PNG/JPG/JPEG 格式!");
            }
            return isIMG;
        },
        upload()
        {
            let formData = new FormData();
            formData.append("file", this.uploadFile);
            this.$emit("uploadImg", formData, this.id);
        },
        remove()
        {
            // 图片设为空
            this.defaultImg = "";
            // 隐藏图片层，展示选择图片层
            this.operationShow = false;
            this.$emit("removeImg", this.id);
        },
        init(data)
        {
            if (data !== undefined && data !== "" && data !== null)
            {
                this.operationShow = true;
                this.defaultImg = process.env.BASE_API + "/service-admin/" + data;
            }
            else
            {
                this.defaultImg = "";
                this.operationShow = false;
            }
        },
        showBigImg()
        {
            if (this.needShowBigImg)
            {
                this.dialogBigImgVisible = true;
            }
        }
    }
};
</script>
<style   rel="stylesheet/scss" lang="scss" >
@mixin base{
    position: absolute;
    top:5%;
    height: 90%;
    width: 100%;
}
.dropzone{
    position: relative;
    height: 200px;
    max-width: 200px;
    width:100%;
    border-radius: 8px;
    border: 1px dashed #ccc;
    box-sizing: border-box;
    input{
        @include base;
        opacity: 0;
        z-index: 10;
    }
    .operation-div{
        @include base;
        z-index: 100;
        text-align: center;
        img{
            width: 80%;
            height: 80%;
        }
        &:hover>.remove{
            display: block;
        }
        .remove{
            display: block;
            height: 10%;
            width: 100%;
            display: none;
            text-align: center;
            cursor: pointer;
        }
        .remove:hover{
            color: #409EFF;
        }
    }
    .txt{
        position: absolute;
        width: 100%;
        height: 30px;
        font-size: 40px;
        line-height: 30px;
        top: 50%;
        transform: translateY(-50%);
        text-align: center;
        color: #aaa;
        z-index: 1;
        .info{
            font-size: 10px;
            color: #999;
            display: block;
            width: 100%;
            text-align: center;
        }
    }
    .hide{
        display: none;
    }

}
</style>

