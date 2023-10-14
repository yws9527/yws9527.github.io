## Vue3模板片段 - vscode



> ```json
> {
>     "vue3 template": {
>        "prefix": "vue3-sfc",
>        "description": "vue3模板",
>        "body": [
>          "${BLOCK_COMMENT_START}",
>          " * @module: $1",
>          " * @author: ${2:anonymous}",
>          " * @since: ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE}",
>          " * @file: ${TM_FILENAME}",
>          "${BLOCK_COMMENT_END}",
>          "<template>",
>          "  <div class=\"\">",
> 			"    $3",
> 			"  </div>",
>          "</template>\n",
>          "<script setup lang=\"ts\">",
> 			"  import { ref } from 'vue';\n",
>          "  $4",
> 			"</script>\n",
>          "<style lang=\"\" scoped>",
>          "  ",
>          "</style>\n"
>        ]
>     }
> }
> ```
>
> 