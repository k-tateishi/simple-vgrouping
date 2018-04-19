<template>
  <div>
    <div class="columns">
      <div class="column">
        <label class="label">メンバー名
          &nbsp;&nbsp;
          <input type="checkbox" @click="toggleInputMode">
          テキストエリア
        </label>
        <div v-show="inputMode">
          <div class="field">
            <div class="control">
              <textarea class="textarea is-info" type="text" v-model="textareaUsers"></textarea>
            </div>
          </div>
        </div>
        <div v-show="!inputMode">
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
        <div class="has-text-left has-text-danger is-size-6" v-show="isMemberError">
          メンバーを入力して下さい。
        </div>
        <div class="has-text-left has-text-danger is-size-6" v-show="isMemberAddError">
          メンバー名が重複しています。
        </div>
      </div>

      <div class="column">
        <label class="label">グループ数</label>
        <input class="input" type="text" placeholder="3" v-model="groupNumber">
        <div class="has-text-left has-text-danger" v-show="isGroupNumberError">
          グループ数を半角整数で入力して下さい。
        </div>
      </div>

      <div class="column">
        <label class="label">&nbsp;</label>
        <button class="button is-info" @click="makeGroup">この条件で班分けする！</button>
      </div>
    </div>

    <div class="columns" v-for="idx in range(0, Math.ceil(makedGroupsList.length / 3)-1)" :key="idx">
      <div class="column" v-for="gidx in range(0, 2)" :key="gidx">
        <div class="card" v-bind:class="{'is-invisible': !makedGroupsList[gidx + idx * 3]}">
          <header class="card-header">
            <p class="card-header-title">
              {{ makedGroupsList[gidx + idx * 3] }}
            </p>
          </header>
          <div class="card-content">
            <div class="content has-text-left" v-for="groupedUser in groupedUsersList(makedGroupsList[gidx + idx * 3])" :key="groupedUser.id">
              {{ groupedUser.user }}
            </div>
            <div class="content has-text-danger" v-show="groupedUsersList(makedGroupsList[gidx + idx * 3]).length === 0">
              メンバーがいません
            </div>
          </div>
          <footer class="card-footer">
          </footer>
        </div>
      </div>
    </div>
    <div v-show="data2Csv">
      <label class="label has-text-left">CSVデータ</label>
      <div class="field">
        <div class="control">
          <textarea class="textarea is-warning" type="text" v-model="data2Csv" readonly></textarea>
        </div>
      </div>
      <button class="button is-warning">csvダウンロード</button>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'grouping',
    data () {
      return {
        users: [],
        userName: '',
        groupNumber: null,
        makedGroups: [],
        groupedUsers: [],
        inputMode: false,
        textareaUsers: '',
        isMemberError: false,
        isGroupNumberError: false,
        isMemberAddError: false
      }
    },
    methods: {
      addUser() {
        if (this.users.indexOf(this.userName) > 0) {
          this.isMemberAddError = true;
          return;
        } else if (this.users.indexOf(this.userName) === 0) {
          // only blank duplicate
          return;
        }
        this.isMemberAddError = false;
        this.users.push(this.userName);
        this.userName = '';
      },
      deleteUser(name) {
        var deleted = this.users.filter((e) => {
          if (e !== name) {
            return e;
          }
        });
        this.users = deleted;
      },
      initializeGroupNumber() {
        if (!this.validNumber || this.groupNumber === 0) {
          this.groupNumber = 1;
        }
      },
      makeGroup() {
        this.isMemberAddError = false;
        if (!this.inputValidation) {
          this.showErrorMessage();
          return;
        } else {
          this.isMemberError = false;
          this.isGroupNumberError = false;
        }
        this.syncUsers();
        this.makedGroups = [];
        this.groupedUsers = [];
        // Group names
        this.initializeGroupNumber();
        for (var i=1; i<=this.groupNumber; i++) {
          this.makedGroups.push('グループ' + i);
        }

        // Make random users
        var userIndex = this.users.length;
        var randomUserIndex = [];
        var indexList = [];
        for (var j=0; j<userIndex; j++) {
          indexList.push(j);
        }
        for (var k=0; k<this.users.length; k++) {
          var rand = Math.floor(Math.random() * userIndex);
          userIndex -= 1;
          var temp = indexList[userIndex];
          indexList[userIndex] = indexList[rand];
          indexList[rand] = temp;
          randomUserIndex.push(indexList[userIndex]);
        }

        // Grouped users
        var groupNumberIndex = 1;
        for (var l=0; l<this.users.length; l++) {
          var groupedUser = {};
          groupedUser.id = l;
          groupedUser.group = 'グループ' + groupNumberIndex;
          groupedUser.user = this.users[randomUserIndex[l]];
          this.groupedUsers.push(groupedUser);
          groupNumberIndex++;
          if (groupNumberIndex > this.groupNumber) {
            groupNumberIndex = 1;
          }
        }
      },
      groupedUsersList(groupName) {
        if (!groupName || groupName.length <= 0) {
          return [];
        }
        return this.groupedUsers.filter((e) => {
          if (e && e.group === groupName) {
            return e;
          }
        });
      },
      range(from, to) {
        var array = [];
        for (var i=from; i<=to; i++) {
          array.push(i)
        }
        return array;
      },
      toggleInputMode() {
        this.isMemberAddError = false;
        this.syncUsers();
        this.inputMode = !this.inputMode;
      },
      syncUsers() {
        if (this.inputMode) {
          var textareaList = this.textareaUsers.split('\n');
          this.users = textareaList.filter((x, i, self) => {
              // delete empty and duplicate
              return self && self.length > 0 && self.indexOf(x) === i;
          });
        } else {
          this.textareaUsers = '';
          for (var user in this.users) {
            this.textareaUsers += user;
            this.textareaUsers += '\n';
          }
        }
      },
      showErrorMessage() {
        if (!this.validUsers || !this.validUsers) {
          this.isMemberError = true;
        } else {
          this.isMemberError = false;
        }
        if (!this.validNumber) {
          this.isGroupNumberError = true;
        } else {
          this.isGroupNumberError = false;
        }
      }
    },
    computed: {
      usersList: function() {
        return this.users;
      },
      validUsers: function() {
        return this.users && this.users.length > 0;
      },
      validTextarea: function() {
        return this.textareaUsers && this.textareaUsers.length > 0;
      },
      validNumber: function() {
        return this.groupNumber && this.groupNumber.search(/^[0-9]+$/) === 0;
      },
      inputValidation: function() {
        return (this.validUsers || this.validTextarea) && this.validNumber;
      },
      makedGroupsList: function() {
        return this.makedGroups;
      },
      data2Csv: function() {
        var csv = '';
        for (var i=0; i<this.makedGroups.length; i++) {
          csv += this.makedGroups[i];
          for (var j=0; j<this.groupedUsersList(this.makedGroups[i]).length; j++) {
            csv += ', ';
            csv += this.groupedUsersList(this.makedGroups[i])[j].user;
          }
          csv += '\n';
        }
        return csv;
      }
    }
  }
</script>
