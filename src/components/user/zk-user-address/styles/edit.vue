<template>
  <view class="user-add-edit" v-if="async">
    <view class="input_boxs">
      <div class="item">
        <div class="label">姓名：</div>
        <div class="address-content">
          <input class="phopes " placeholder="请输入姓名" v-model="addressInput.name">
        </div>
      </div>
      <div class="item">
        <div class="label">电话：</div>
        <div class="address-content">
          <input class="phopes vercate" placeholder="请输入电话" v-model="addressInput.mobile">
        </div>
      </div>
      <div class="item">
        <x-region v-model="addressInput.regionId"></x-region>
      </div>
      <div class="item">
        <div class="label">详细地址：</div>
        <div class="address-content">
          <input class="phopes vercate" placeholder="请输入详细地址" v-model="addressInput.address">
        </div>
      </div>
      <div class="item">
        <div class="label">是否默认：</div>
        <div class="address-content switch-box">
          <div class="switch-box_poist" :class="{'check_poist':addressInput.isDefault==true}" @click="addressInput.isDefault = !addressInput.isDefault">
            <span class="switch_circle"></span>
          </div>
        </div>
      </div>
    </view>
    <div></div>
    <view class="address-edit_btn">
      <div class="edit_btn" @click="sumbit()">确定</div>
    </view>
  </view>
</template>


<script>
  import styleApi from './styleApi'
  export default {
    data () {
      return {
        themeColor: '#007AFF',
        pickerValue: '请选择地区',
        cityPickerValueDefault: [0, 0, 0], // 默认地址
        addressInput: {
          name: '',
          mobile: '',
          address: '',
          isDefault: false,
          userId: '',
          regionId: '',
          type: 1
        },
        pagesId: null,
        formType: null, // 添加成功后的返回页面，默认为list, 
        async: false
      }
    },
    methods: {
      async init (data, formType) {
        this.formType = formType
        var user = this.$user.loginUser()
        this.addressInput = {
          name: user.name,
          mobile: user.mobile,
          userId: user.id,
          isDefault: false
        }
        if (data) {
          this.pagesId = data.id
          this.addressInput = await styleApi.editAddress(this, data)
        }
        this.async = true
      },
      async sumbit () {
        this.addressInput.userId = this.$user.loginUser().id
        if (this.pagesId) { this.addressInput.id = this.pagesId }
        var response = await this.$api.httpPost('/Api/UserAddress/SaveOrderAddress', this.addressInput)
        if (response.status === 1) {
          this.$api.toastSuccess('添加成功')
          if (await this.$api.localGet('addressJump')) {
            setTimeout(() => {
              this.$api.to(this.$api.localGet('addressJump'))
              this.$api.localRemove('addressJump')
            }, 500)
          } else {
            this.$api.toastSuccess('添加成功')
            setTimeout(() => {
              if (this.formType) {
                this.$emit('change', { type: this.formType })
              } else {
                this.$api.back()
              }
            }, 300)
          }
        } else {
          this.$api.toastWarn(response.message)
        }
      }
    }
  }
</script>


<style lang="scss" scoped>
  @import "../css/edit.scss";
</style>
