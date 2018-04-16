<template>
    <footer class="footer">
      <div class="container">
        <div class="content has-text-centered">
          <p>
            <span>
              Copyright <i class="far fa-copyright"></i> {{ year }} k-tateishi All Rights Reserved.
            </span>
          </p>
        </div>
      </div>
    </footer>
</template>

<script>
  export default {
    name: 'footer-component',
    data () {
      return {
        year: new Date().getFullYear()
      }
    }
  }
</script>
