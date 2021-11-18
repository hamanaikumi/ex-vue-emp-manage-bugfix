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
            <div>{{ errorZipcode }}</div>
            <input
              id="zipcode"
              type="text"
              class="validate"
              v-model.number="zipcode"
              required
            />
            <label for="zipcode">郵便番号</label>
          </div>
          <button
            type="button"
            class="btn btn-large btn-serch waves-effect waves-light"
            @click="getAddress"
          >
            検索
          </button>
        </div>
        <div class="row">
          <div class="input-field col s12">
            <div>{{ errorAddress }}</div>
            <input
              id="address"
              type="text"
              class="validate"
              v-model="address"
              required
            />
            <label for="address">住所</label>
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
  // 住所
  private address = "";
  // 郵便番号
  private zipcode = "";
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
  //住所エラーメッセージ
  private errorAddress = "";
  //郵便番号エラーメッセージ
  private errorZipcode = "";
  //パスワード用エラーメッセージ
  private errorPassword = "";

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
    if (this.zipcode == "") {
      this.errorZipcode = "入力してください";
    }
    if (this.address == "") {
      this.errorAddress = "入力してください";
    }
    if (this.password == "") {
      this.errorPassword = "入力してください";
    }
    if (
      this.firstName != "" &&
      this.lastName != "" &&
      this.mailAddress != "" &&
      this.password != ""
    ) {
      const response = await axios.post(`${config.EMP_WEBAPI_URL}/insert`, {
        name: this.lastName + " " + this.firstName,
        mailAddress: this.mailAddress,
        password: this.password,
        address: this.address,
      });
      console.dir("response:" + JSON.stringify(response));

      if (response.data.status === "success") {
        this.$router.push("/loginAdmin");
      } else {
        this.errorRegister = "登録できませんでした";
      }
    }
  }
  // 郵便番号から住所を検索し返す
  async getAddress(): Promise<void> {
    // eslint-disable-next-line @typescript-eslint/no-var-requires
    const axiosJsonpAdapter = require("axios-jsonp");

    const zipcodeResponse = await axios.get("https://zipcoda.net/api", {
      adapter: axiosJsonpAdapter,
      params: {
        zipcode: this.zipcode,
      },
    });
    console.dir("zipcodeResponse:" + JSON.stringify(zipcodeResponse));
    this.address =
      zipcodeResponse.data.items[0].pref +
      zipcodeResponse.data.items[0].address;
    console.dir(JSON.stringify(this.address));
  }
}
</script>

<style scoped>
.register-page {
  width: 600px;
}
</style>
