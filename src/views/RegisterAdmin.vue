<template>
  <div class="container">
    <div class="row register-page">
      <form class="col s12" id="reg-form">
        <div class="row">
          <div class="input-field col s6">
            <div>{{ errorLastName }}</div>
            <input
              id="last_name"
              type="text"
              class="validate"
              v-model="lastName"
              required
            />
            <label for="last_name">姓</label>
          </div>
          <div class="input-field col s6">
            <div>{{ errorFirstName }}</div>
            <input
              id="first_name"
              type="text"
              class="validate"
              v-model="firstName"
              required
            />
            <label for="first_name">名</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <div>{{ errorMailAddress }}</div>
            <input
              id="email"
              type="email"
              class="validate"
              v-model="mailAddress"
              required
            />
            <label for="email">メールアドレス</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <div>{{ errorPassword }}</div>
            <input
              id="password"
              type="password"
              class="validate"
              minlength="8"
              v-model="password"
              required
            />
            <label for="password">パスワード</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <div>{{ errorConfirmPassword }}</div>
            <input
              id="password"
              type="password"
              class="validate"
              minlength="8"
              v-model="confirmPassword"
              required
            />
            <label for="password">確認用パスワード</label>
          </div>
        </div>
        <div class="row">
          <div class="input-field col s6">
            <div>{{ errorRegister }}</div>
            <button
              class="btn btn-large btn-register waves-effect waves-light"
              type="button"
              v-on:click="registerAdmin"
            >
              登録
              <i class="material-icons right">done</i>
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import config from "@/const/const";
import axios from "axios";

/**
 * 管理者登録をする画面.
 */
@Component
export default class RegisterAdmin extends Vue {
  // 姓
  private lastName = "";
  // 名
  private firstName = "";
  // メールアドレス
  private mailAddress = "";
  // パスワード
  private password = "";
  // 登録エラー時のメッセージ
  private errorRegister = "";
  //姓エラーメッセージ
  private errorFirstName = "";
  //名エラーメッセージ
  private errorLastName = "";
  //メール用エラーメッセージ
  private errorMailAddress = "";
  //パスワード用エラーメッセージ
  private errorPassword = "";
  // 確認用パスワード
  private confirmPassword = "";
  //確認用パスワードエラーメッセージ
  private errorConfirmPassword = "";

  /**
   * 管理者情報を登録する.
   *
   * @remarks
   * 本メソッドは非同期でWebAPIを呼び出し管理者登録をするためasync/await axiosを利用しています。これらを利用する場合は明示的に戻り値にPromiseオブジェクト型を指定する必要があります。
   * @returns Promiseオブジェクト
   */
  async registerAdmin(): Promise<void> {
    // 管理者登録処理
    if (this.firstName == "") {
      this.errorFirstName = "入力してください";
    }
    if (this.lastName == "") {
      this.errorLastName = "入力してください";
    }
    if (this.mailAddress == "") {
      this.errorMailAddress = "入力してください";
    }
    if (this.password == "") {
      this.errorPassword = "入力してください";
    }
    if (this.confirmPassword == "") {
      this.errorConfirmPassword = "入力してください";
    }
    if (this.password !== this.confirmPassword) {
      this.errorConfirmPassword = "パスワードが一致しません";
      return;
    } else if (
      this.firstName != "" &&
      this.lastName != "" &&
      this.mailAddress != "" &&
      this.password != "" &&
      this.confirmPassword != ""
    ) {
      const response = await axios.post(`${config.EMP_WEBAPI_URL}/insert`, {
        name: this.lastName + " " + this.firstName,
        mailAddress: this.mailAddress,
        password: this.password,
      });
      console.dir("response:" + JSON.stringify(response));

      if (response.data.status === "success") {
        this.$router.push("/loginAdmin");
        console.log("成功");
      } else {
        this.errorRegister = "登録できませんでした";
        console.log("失敗");
      }
    }
  }
}
</script>

<style scoped>
.register-page {
  width: 600px;
}
</style>
