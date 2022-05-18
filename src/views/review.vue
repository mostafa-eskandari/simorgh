<script>
// صدا زدن در mounted
export default {        
    mounted() {
      this.loadData();
    },
    methods: {
        loadData() {
            // This syntax may be wrong, too. But the function isn't
            // even running, so I haven't started to debug this yet
            this.$http.get("https://icanhazip.com")
                .then(xhr => this.text = xhr.body);
        }
    }
};
</script>
