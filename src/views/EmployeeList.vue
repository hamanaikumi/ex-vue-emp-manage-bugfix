<template>
  <div class="container">
    <!-- パンくずリスト -->
    <nav>
      <div class="nav-wrapper">
        <div class="col s12 teal">
          <a class="breadcrumb">従業員リスト</a>
        </div>
      </div>
    </nav>
    <div>従業員数:{{ getEmployeeCount }}人</div>
    <div class="row">
      <table class="striped">
        <thead>
          <tr>
            <th>名前</th>
            <th>入社日</th>
            <th>扶養人数</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="employee of currentEmployeeList" v-bind:key="employee.id">
            <td>
              <router-link :to="'/employeeDetail/' + employee.id">{{
                employee.name
              }}</router-link>
            </td>
            <td>{{ employee.formatHireDate }}</td>
            <td>{{ employee.dependentsCount }}人</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="page-button">
      <button
        type="button"
        v-for="page of pages"
        v-bind:key="page"
        @click="onclickPage(page)"
      >
        {{ page }}
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { Employee } from "@/types/employee";
/**
 * 従業員一覧を表示する画面.
 */
@Component
export default class EmployeeList extends Vue {
  // 従業員一覧
  private currentEmployeeList: Array<Employee> = [];
  // 従業員数
  private employeeCount = 0;
  // ページ数
  private pages: Array<number> = [];

  /**
   * Vuexストアのアクション経由で非同期でWebAPIから従業員一覧を取得する.
   *
   * @remarks
   * Vueインスタンスが生成されたタイミングで
   * Vuexストア内のアクションを呼ぶ(ディスパッチする)。
   * ライフサイクルフックのcreatedイベント利用。
   *
   * 取得してからゲットするため、async awaitを利用している。
   */
  async created(): Promise<void> {
    await this.$store.dispatch("getEmployeeList");

    // 従業員一覧情報をVuexストアから取得
    // 非同期で外部APIから取得しているので、async/await使わないとGetterで取得できない
    // ページング機能実装のため最初の10件に絞り込み
    this.currentEmployeeList = this.$store.getters.getAllEmployees.slice(0, 10);
    console.log("最初の10件：" + JSON.stringify(this.currentEmployeeList));
    let pageNumber = Math.ceil(
      Number(this["$store"].getters.getAllEmployeeCount) / 10
    );
    for (let i = 1; i <= pageNumber; i++) {
      this.pages.push(i);
    }
  }
  /**
   * 現在表示されている従業員一覧の数を返す.
   *
   * @returns 現在表示されている従業員一覧の数
   */
  get getEmployeeCount(): number {
    return this.$store.getters.getAllEmployees.length;
  }

  /**
   * クリックされたページに対応する従業員一覧を表示する.
   */
  onclickPage(page: number): void {
    this.currentEmployeeList = this.$store.getters.getAllEmployees.slice(
      page * 10 - 10,
      page * 10
    );
    console.log(this.currentEmployeeList);
  }
}
</script>

<style scoped>
.searchForm {
  margin-bottom: 20px;
  width: 450px;
  margin: 0 auto;
}

.searchBtn {
  display: block;
  width: 150px;
  margin: 0 auto;
}

.page-button {
  text-align: center;
  margin-bottom: 10px;
}
</style>
