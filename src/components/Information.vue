<template>
    <div class="container">
      <br />
      <div class="content has-text-left">
        <strong class="h2 is-size-5">メンバー</strong>
        <p class="is-size-10">メンバー入力欄にグループメンバーの名前を入力して下さい。</p>
        <p class="is-size-10">同名は入力できません。</p>
      </div>
      <div class="content has-text-left">
        <strong class="h2 is-size-5">グループ数</strong>
        <p class="is-size-10 ">グループ数を半角数字で入力して下さい。</p>
        <p class="is-size-10">グループ数がメンバー数よりも大きい場合はメンバーが存在しないグループが作成されます。</p>
      </div>
      <br />
    </div>
</template>

<script>
  export default {
    name: 'information-component'
  }
</script>
