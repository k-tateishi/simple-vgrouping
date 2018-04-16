<template>
    <div class="columns">
      <div class="column">
        <label class="label">メンバー名</label>
        <div class="field has-addons">
          <div class="control has-icons-left">
            <input class="input" type="text" placeholder="山田 太郎" v-model="userName">
            <span class="icon is-small is-left">
              <i class="far fa-user"></i>
            </span>
          </div>
          <div class="control">
            <a class="button is-info" @click="addUser">
              追加
            </a>
          </div>
        </div>
        <div v-for="name in usersList" :key="name">
          <div v-show="name">{{ name }} <a class="delete" @click="deleteUser(name)"></a></div>
        </div>
      </div>

      <div class="column">
        <label class="label">グループ名</label>
        <div class="field has-addons">
          <div class="control has-icons-left">
            <input class="input" type="text" placeholder="グループA" v-model="groupName">
            <span class="icon is-small is-left">
              <i class="far fa-user"></i>
            </span>
          </div>
          <div class="control">
            <a class="button is-info" @click="addGroup">
              追加
            </a>
          </div>
        </div>
        <div v-for="groupName in groupsList" :key="groupName">
          <div v-show="groupName">{{ groupName }} <a class="delete" @click="deleteGroup(groupName)"></a></div>
        </div>
      </div>

      <div class="column">
        <label class="label">グループ数</label>
        <input class="input" type="text" placeholder="3" v-model="groupNumber">
      </div>

      <div class="column">
        <label class="label">&nbsp;</label>
        <button class="button is-info" v-show="inputValidation">この条件で班分けする！</button>
        <button class="button is-info" v-show="!inputValidation" disabled>条件を設定してください</button>
      </div>
    </div>
</template>

<script>
  export default {
    name: 'grouping',
    data () {
      return {
        users: [],
        userName: "",
        groups: [],
        groupName: "",
        groupNumber: null
      }
    },
    methods: {
      addUser() {
        if (this.users.indexOf(this.userName) >= 0) {
          // TODO: エラーメッセージを出す
          return;
        }
        this.users.push(this.userName);
        this.userName = "";
      },
      deleteUser(name) {
        var deleted = this.users.filter((e) => {
          if (e !== name) {
            return e;
          }
        });
        this.users = deleted;
      },
      addGroup() {
        if (this.groups.indexOf(this.groupName) >= 0) {
          // TODO: エラーメッセージを出す
          return;
        }
        this.groups.push(this.groupName);
        this.groupName = "";
      },
      deleteGroup(name) {
        var deleted = this.groups.filter((e) => {
          if (e !== name) {
            return e;
          }
        });
        this.groups = deleted;
      }
    },
    computed: {
      usersList: function() {
        return this.users;
      },
      groupsList: function() {
        return this.groups;
      },
      validUsers: function() {
        return this.users && this.users.length > 0;
      },
      validGroups: function() {
        return this.groups && this.groups.length > 0;
      },
      validNumber: function() {
        return this.groupNumber && this.groupNumber.search(/^[0-9]+$/) === 0;
      },
      inputValidation: function() {
        return this.validUsers && this.validGroups && this.validNumber;
      }
    }
  }
</script>
