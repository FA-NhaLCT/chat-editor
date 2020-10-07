<template>
  <div
    class="chat-composer"
    id="inputComposer"
    ref="inputComposer"
    contenteditable
    @input="handleInput"
    @keydown="handleKeyDown"
    @keyup="handleKeyUp"
  ></div>
</template>

<script>
export default {
  name: "NewComposer",
  data() {
    return {
      data: [{}],
      recoverData: null,
      status: null,
    };
  },
  mounted() {
    // const inputComposer = this.$refs.inputComposer;
    // const newLineDiv = document.createElement("div");
    // newLineDiv.setAttribute("id", `input-line-${++this.line}`);
    // inputComposer.appendChild(newLineDiv);
    this.loadDom();
  },
  methods: {
    setCaret(el, pos) {
      let range = document.createRange();
      let sel = window.getSelection();

      range.setStart(el, pos);
      range.collapse(true);

      sel.removeAllRanges();
      sel.addRange(range);
    },
    loadDom() {
      const inputComposer = this.$refs.inputComposer;
      inputComposer.innerHTML = "";
      let LineDiv, PartDiv;
      this.data.forEach((item, index) => {
        LineDiv = document.createElement("div");
        LineDiv.style.width = "100%";
        LineDiv.setAttribute("id", `input-line`);
        inputComposer.appendChild(LineDiv);
      });
    },
    handleKeyuP(e) {
      // console.log(e);
      // if (e.keyCode == 13 && e.shiftKey) {
      //   console.log("keydown linebreak");
      // }
    },
    handleInput(e) {
      console.log(e);
      this.checkEmptyCompose();
      const sel = window.getSelection();
      this.checkBr();
      //   if (e.inputType==='insertLineBreak') {
      //       console.log('gau gau')
      //   }
    },
    checkBr() {
      const inputComposer = this.$refs.inputComposer;
      Array.from(inputComposer.childNodes).forEach((item) => {
        console.log({ item: item.childNodes });
        if (
          item.childNodes.length > 0 &&
          item.childNodes[0].nodeName === "BR"
        ) {
          console.log("remove");
          item.removeChild(item.childNodes[0]);
        }
      });
    },
    handleKeyUp(e) {},
    handleKeyDown(e) {
      const inputComposer = this.$refs.inputComposer;
      // console.log(e);
      if (e.keyCode == 13 && e.shiftKey) {
        console.log("break line");
        // Gọt data
        this.handleNewLine();
      }
    },
    handleNewLine() {
      // Gọt data
      const inputComposer = this.$refs.inputComposer;
      const sel = window.getSelection();
      const { anchorNode } = sel;
      let lineNode, lineDiv;
      let node = anchorNode;
      lineDiv = document.createElement("div");
      lineDiv.style.width = "100%";
      lineDiv.setAttribute("id", `input-line`);
      console.log({ sel, node });
      console.log(
        "cut",
        anchorNode.cloneNode().nodeValue.slice(sel.anchorOffset)
      );
      console.log(sel.anchorOffset);
      if (anchorNode.nodeName === "#text") {
        const textNode = document.createTextNode(
          anchorNode.cloneNode().nodeValue.slice(sel.anchorOffset)
        );
        // anchorNode.nodeValue.replace(textNode.value,'')
        const indexAnchorNode = Array.from(inputComposer.childNodes).indexOf(
          anchorNode.parentElement
        );
        console.log("index", indexAnchorNode);
        lineDiv.appendChild(textNode);

        //Add line mới
        if (inputComposer.childNodes.length === 1) {
          inputComposer.appendChild(lineDiv);
        } else if (inputComposer.childNodes.length > 1) {
          inputComposer.insertBefore(
            lineDiv,
            inputComposer.childNodes[indexAnchorNode + 1]
          );
        }
        this.setCaret(lineDiv, 0);
      }
      this.status = "newLine";
      // anchorNode.removeChild(
      //   anchorNode.childNodes[anchorNode.childNodes.length - 1]
      // );
      // anchorNode.removeChild(
      //   anchorNode.childNodes[anchorNode.childNodes.length - 1]
      // );
    },
    checkEmptyCompose() {
      //Luôn luôn tạo div cho dòng mới phòng trường hợp user dùng backspace clear content
      const inputComposer = this.$refs.inputComposer;
      if (inputComposer.childNodes.length == 0) {
        this.line = 0;
        const newLineDiv = document.createElement("div");
        newLineDiv.setAttribute("id", `input-line`);
        newLineDiv.style.width = "100%";
        inputComposer.appendChild(newLineDiv);
      }
    },
  },
};
</script>

<style lang="scss">
body {
  padding: 0;
  margin: 0;
}
.chat-composer {
  margin: 0 auto;
  text-align: left;
  width: 500px;
  display: flex;
  flex-wrap: wrap;
  // flex-direction: column;
  outline: none;
  > div {
    background: pink;
  }
}
</style>