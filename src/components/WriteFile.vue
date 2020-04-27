<template>
  <el-main>
    <!-- ファイル名入力欄 -->
    <el-input placeholder="ファイル名を入力してください。" v-model="input" class="input-with-select">
      <el-select v-model="select" slot="append" placeholder="Select">
        <el-option label=".md" value=".md"></el-option>
        <el-option label=".txt" value=".txt"></el-option>
      </el-select>
    </el-input>

    <!-- 本文入力欄 -->
    <el-input
      type="textarea"
      :rows="2"
      :autosize="{ minRows: 10, maxRows: 50}"
      placeholder="Please input"
      v-model="textarea">
    </el-input>

    <!-- ファイル読み込みボタン -->
    <el-button type="primary" @click="readTempleteFile()">ファイル読み込み</el-button>
    <!-- ファイル書き出しボタン -->
    <el-button type="success" @click="writeFile()">ファイル書き出し</el-button>
  </el-main>
</template>

<script>
  export default {
    data () {
      return {
        taskList: [],
        input: '',
        select: '.md',
        textarea: ''
      }
    },
    methods: {
      // テンプレートファイル読み込み
      readTempleteFile(){

        // 初期化
        this.textarea=""

        // 現在日付取得
        var dt = new Date();
        var yyyy = dt.getFullYear();
        var mm= ("00" + (dt.getMonth()+1)).slice(-2);
        var dd = ("00" + dt.getDate()).slice(-2);
        var dayOfWeek = dt.getDay() ; // 曜日(数値)
        var dayOfWeekStr = [ "日", "月", "火", "水", "木", "金", "土" ][dayOfWeek]; // 曜日(日本語表記)

        // パス設定
        const path = require('path');
        const basePath = process.cwd();
        const templeteFilePath = path.resolve(basePath, './resource/templete.md');

        // テンプレートファイル読み込み
        const fs = require('fs');
        var readline = require("readline");
        var stream = fs.createReadStream(templeteFilePath, "utf8");
        var reader = readline.createInterface({ input: stream });

        let isFileNameFlg =true;
        reader.on("line", (line) => {

          // ======= 変換仕様 =======
          // 日付変換（yyyymmdd）
          if ( line.match(/yyyymmdd/)) {
            var replaceStr =  yyyy + mm + dd;
            line = line.replace( "yyyymmdd", replaceStr );
          }

          // 日付変換（yyyy年mm月dd日（Day））
          if ( line.match(/yyyy年mm月dd日（Day）/)) {
            replaceStr =  yyyy + "年"+ mm + "月" + dd + "日" + "（" + dayOfWeekStr + "）";
            line = line.replace( "yyyy年mm月dd日（Day）", replaceStr );
          }

          // 1行目の判定
          if (isFileNameFlg) {
            this.input = line;
            isFileNameFlg =false;
          }else{
            this.textarea += line
            this.textarea += '\n'
          }
        });
      },
      // ファイル書き出し
      writeFile () {
        const fs = require('fs');
        fs.writeFileSync(this.input +this.select, this.textarea);

        this.$message({
          message: 'ファイルが正常に出力されました。',
          type: 'success'
        });
      },
    }
  }
</script>

<style scoped>
  main.el-main {
    width: 600px;
    margin: 0px auto;
  }
</style>

<style>
  .el-select .el-input {
    width: 70px;
  }
  .input-with-select .el-input-group__prepend {
    background-color: #fff;
  }
</style>