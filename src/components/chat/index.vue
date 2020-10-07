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
      lastEditRange: null,
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
    getCursorPos() {
      this.lastEditRange = window.getSelection().getRangeAt(0);
    },
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
      this.getCursorPos();
      this.checkEmptyCompose();
      const sel = window.getSelection();
      this.checkBr();
      //   if (e.inputType==='insertLineBreak') {
      //       console.log('gau gau')
      //   }
    },
    checkBr() {
      const inputComposer = this.$refs.inputComposer;
      Array.from(inputComposer.childNodes).forEach(item => {
        if (
          item.childNodes.length === 1 &&
          item.childNodes[0].data === "" &&
          item.childNodes[0].nodeName == "#text"
        ) {
          item.appendChild(document.createElement("br"));
        }
        if (
          item.childNodes.length === 2 &&
          item.childNodes[0].nodeName === "BR" &&
          item.childNodes[1].nodeName === "BR"
        ) {
          item.removeChild(item.childNodes[1]);
        }
        if (
          item.childNodes.length >= 2 &&
          item.childNodes[0].nodeName === "BR" &&
          item.childNodes[1].nodeName !== "BR"
        ) {
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
    findInputLine(node) {
      if (node.id !== "input-line") {
        return this.findInputLine(node.parentElement);
      } else return node;
    },
    isEndOfLine(sel) {
      const { anchorNode, anchorOffset } = sel;
      const node = this.findInputLine(anchorNode);
      if (anchorNode.id === "input-line") return true;
      else
        return (
          Array.from(anchorNode.parentElement.childNodes).indexOf(
            anchorNode
          ) ===
            anchorNode.parentElement.childNodes.length - 1 &&
          anchorNode.length === anchorOffset
        );
    },
    addLine(lineDiv,indexAnchorNode) {
      const inputComposer = this.$refs.inputComposer;
      if (inputComposer.childNodes.length === 1) {
        inputComposer.appendChild(lineDiv);
      } else if (inputComposer.childNodes.length > 1) {
        inputComposer.insertBefore(
          lineDiv,
          inputComposer.childNodes[indexAnchorNode + 1]
        );
      }
    },
    handleNewLine() {
      // Gọt data
      const inputComposer = this.$refs.inputComposer;
      const sel = window.getSelection();
      const { anchorNode, anchorOffset } = sel;
      let lineNode, lineDiv;
      lineNode = this.findInputLine(anchorNode);
      let node = anchorNode;
      lineDiv = document.createElement("div");
      lineDiv.style.width = "100%";
      lineDiv.setAttribute("id", `input-line`);
      const indexAnchorNode = Array.from(inputComposer.childNodes).indexOf(
        lineNode
      );
      //Case end of line
      const isEndOfLine = this.isEndOfLine(sel);
      if (isEndOfLine) {
        lineDiv.appendChild(document.createElement("br"));
        if (anchorNode.parentNode.childNodes.length === 0)
          anchorNode.parentNode.appendChild(document.createElement("br"));
        //Add line mới
        this.addLine(lineDiv,indexAnchorNode)
        this.setCaret(lineDiv, 1);
      } else if (anchorNode.nodeName === "#text") {
        const textNode = document.createTextNode(
          anchorNode.cloneNode().nodeValue.slice(sel.anchorOffset)
        );
        anchorNode.nodeValue = anchorNode.nodeValue.substring(
          0,
          sel.anchorOffset
        );
        lineDiv.appendChild(textNode);
        console.log(anchorNode.parentNode);
        if (lineDiv.childNodes.length === 0)
          lineDiv.appendChild(document.createElement("br"));
        //Add line mới
        this.addLine(lineDiv,indexAnchorNode)
        this.setCaret(lineDiv, 0);
      }
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
  watch: {
    lastEditRange() {
      console.log("Last edit range", { lastedit: this.lastEditRange });
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
