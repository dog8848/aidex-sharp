<template>
  <div class="account-settings-info-view">
    <a-row :gutter="16">
      <a-col :md="20" :lg="12" :offset="2">
        <a-form
          layout="vertical"
          :rules="rules"
          :model="user"
          ref="form"
        >
          <a-form-item
            label="昵称"
            ref="nickName"
            prop="nickName"
            :required="true"
          >
            <a-input v-model="user.nickName" placeholder="给自己起个名字" />
          </a-form-item>
          <a-form-item
            label="手机"
            :required="true"
          >
            <a-input v-model="user.phonenumber" placeholder="xxxxxx"/>
          </a-form-item>
          <a-form-item
            label="电子邮件"
            :required="true"
          >
            <a-input v-model="user.email" placeholder="exp@admin.com"/>
          </a-form-item>
          <a-form-item
            label="性别"
            :required="false"
          >
            <a-radio-group v-model="user.sex">
              <a-radio :value="sexValue.men">男</a-radio>
              <a-radio :value="sexValue.women">女</a-radio>
            </a-radio-group>
          </a-form-item>
          <a-form-item>
            <a-button type="primary" @click="submit">保存</a-button>
          </a-form-item>
        </a-form>

      </a-col>
      <a-col :md="20" :lg="6" :offset="2">
        <div class="ant-upload-preview" @click="$refs.modal.edit(1)" >
          <a-icon type="camera" class="upload-icon"/>
          <div class="mask">
            <!-- <a-icon type="plus" /> -->
          </div>
          <img :src="avatar"/>
        </div>
      </a-col>
    </a-row>

    <avatar-modal ref="modal" @ok="setavatar"/>

  </div>
</template>

<script>
import AvatarModal from './AvatarModal'
import { getUserProfile, updateUserProfile } from '@/api/system/user'
import { mapGetters } from 'vuex'

export default {
  components: {
    AvatarModal
  },
  data () {
    return {
      // cropper
      sexValue: {
        men: '0',
        women: '1'
      },
      user: {},
      preview: {},
      option: {
        img: this.avatar,
        info: true,
        size: 1,
        outputType: 'jpeg',
        canScale: false,
        autoCrop: true,
        // 只有自动截图开启 宽度高度才生效
        autoCropWidth: 180,
        autoCropHeight: 180,
        fixedBox: true,
        // 开启宽度和高度比例
        fixed: true,
        fixedNumber: [1, 1]
      },
      rules: {
        nickName: [
          { required: true, message: '请输入昵称', trigger: 'blur' },
          { min: 3, max: 5, message: '长度应为1-5', trigger: 'blur' }
        ]
      }
    }
  },
  computed: {
    ...mapGetters(['nickname', 'avatar'])
  },
  mounted () {
    // this.getUser()
  },
  methods: {
    getUser () {
        getUserProfile().then(response => {
         const dataValue = response.data
         this.user = dataValue.user
         this.roleGroup = dataValue.roleGroup
         this.postGroup = dataValue.postGroup
        })
    },
    setUserInfo (userData) {
      this.user = userData.user
      this.roleGroup = userData.roleGroup
      this.postGroup = userData.postGroup
    },
    setavatar (url) {
      this.option.img = url
    },
    submit () {
      updateUserProfile(this.user).then(response => {
        this.$notification.open({
          message: '提示',
          description:
            '修改成功',
          icon: <a-icon type="check" style="color: #1890FF" />,
          duration: 3
        })
      })
    }
  }
}
</script>

<style lang="less" scoped>

  .avatar-upload-wrapper {
    height: 200px;
    width: 100%;
  }

  .ant-upload-preview {
    position: relative;
    margin: 0;
    width: 100px;
    height:100px;
    border-radius: 50%;
    box-shadow: 0 0 4px #ccc;

    .upload-icon {
      position: absolute;
      opacity: 0;
      top:23px;
      left:23px;
      font-size: 1rem;
      padding: 10px;
      background: rgba(222, 221, 221, 0.65);
      border-radius: 50%;
    }
    &:hover .upload-icon{
      opacity: 1;
    }
    .mask {
      opacity: 0;
      position: absolute;
      background: rgba(0,0,0,0.4);
      cursor: pointer;
      transition: opacity 0.4s;

      &:hover {
        opacity: 1;
      }

      i {
        font-size: 2rem;
        position: absolute;
        top: 50%;
        left: 50%;
        margin-left: -1rem;
        margin-top: -1rem;
        color: #d6d6d6;
      }
    }

    img, .mask {
      width: 100px;
      height:100px;
      border-radius: 50%;
      overflow: hidden;
    }
  }
</style>
