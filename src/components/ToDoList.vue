<template>
  <div class="ToDoList-style">
    <div class="ToDoList">
      <h1>{{ title }}</h1>
      <div>
        <input type="text" placeholder="新增代辦事件" v-model="newItem" @keyup.enter="addNewItem" />
      </div>
      <div class="before-content">
        <!--顯示全部的事件-->
        <button :class="{ active:filter ==='all'}" @click="filter='all'">顯示全部</button>
        <!--顯示未完成事件-->
        <button :class="{ active:filter ==='active'}" @click="filter='active'">未完成</button>
        <!--顯示已完成事件-->
        <button :class="{ active:filter ==='completed'}" @click="filter='completed'">已完成</button>
        <!--清除事件-->
        <span class="clear" @click="clear">清除已完成事項</span>
      </div>
      <div class="content">
        <div class="new-item" v-for="(item, index) in itemsFiltered" :key="item.id">
          <!--完成按鈕-->
          <div class="item-content">
            <input type="checkbox" v-model="item.completed" />
            <!-- 代辦事件-->
            <div class="item-left">
              <p
                class="item-label"
                :class="{completed:item.completed}"
                v-if="!item.editing"
                @dblclick="edititem(item)"
              >{{ item.title }}</p>
              <input
                v-else
                class="item-edit"
                type="text"
                v-model="item.title"
                @blur="doneEdit(item)"
                @keyup.enter="doneEdit(item)"
                @keyup.esc="cancelEdit(item)"
                v-focus
              />
            </div>
            <!--填充物-->
            <div class="spacer"></div>
            <!--這是刪除鍵-->
            <div class="remove-item" @click="removeItem(index)">&times;</div>
          </div>
        </div>
      </div>
      <div class="content-foot">
        <div id="content-foot">
          <label>
            <input type="checkbox" :checked="!anyRemaining" @change="checkAllItems" />完成所有事項
          </label>
        </div>
        <!--填充物-->
        <div class="spacer"></div>
        <div class="undone">{{ remaining }}件未完成</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ToDoList",
  data: function () {
    return {
      title: "Jasmin's Todolist", //標題名稱
      newItem: "",
      id: 3,
      beforeEditCache: "",
      filter: "all",
      //待辦事項列表
      items: [
        {
          id: 1,
          title: "吃飯",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "睡覺",
          completed: false,
          editing: false,
        },
      ],
    };
  },
  computed: {
    remaining() {
      return this.items.filter((item) => !item.completed).length;
    },
    anyRemaining() {
      return this.remaining !== 0;
    },
    //顯示全部 未完成 已完成
    itemsFiltered() {
      if (this.filter == "all") {
        return this.items;
      } else if (this.filter == "active") {
        return this.items.filter((item) => !item.completed);
      } else if (this.filter == "completed") {
        return this.items.filter((item) => item.completed);
      }

      return this.items;
    },
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },
  methods: {
    //點擊添加按鈕,添加新待辦事件
    addNewItem() {
      if (this.newItem.trim().length === 0) {
        return;
      }
      //使用push為數組添加新元素
      this.items.push({
        isDelete: false,
        editing: false,
        id: this.id, //id 唯一且自增
        title: this.newItem, //todo 標題
      });
      //id自增
      this.id++;
      //清除輸入匡
      this.newItem = "";
    },
    //點擊會出現框框
    edititem(item) {
      this.beforeEditCache = item.title;
      item.editing = true;
    },
    doneEdit(item) {
      if (item.title.trim() === "") {
        item.title = this.beforeEditCache;
      }
      item.editing = false;
    },
    cancelEdit(item) {
      item.title = this.beforeEditCache;
      item.editing = false;
    },
    //點擊x會讓整行都消失
    removeItem(index) {
      this.items.splice(index, 1);
    },
    //點擊後會讓框框的勾勾全都取消
    checkAllItems() {
      this.items.forEach((item) => (item.completed = event.target.checked));
    },
    //點擊框框打勾後,點擊清除完成 會讓刪除陣列
    clear() {
      this.items = this.items.filter((item) => !item.completed);
    },
    //點擊顯示全部 顯示全部包含未完成和已完成
    // 點擊未完成顯示未完成事件
    showNotContent() {
      this.items = this.items.filter((item) => {
        return item.isDelete !== true;
      });
    },
    // 點擊已完成顯示已完成事件
    showComplete() {
      this.items = this.items.filter((item) => {
        return item.isDelete !== false;
      });
    },
  },
};
</script>

<style>
.delete-item {
  text-decoration: line-through;
}
.ToDoList-style {
  display: flex;
  justify-content: center;
}
.ToDoList h1 {
  text-align: center;
  color: green;
  margin: 20px 0px;
}
.ToDoList input {
  padding: 5px 0px 5px 5px;
  width: 97%;
}
.before-content {
  margin: 20px 0px;
}
.before-content button {
  border: 2px #2a2a69 none;
  margin: 0px 10px;
  color: green;
  cursor: pointer;
}
.clear {
  font-size: 15px;
  color: #aaaaaa;
  margin-left: 30px;
  cursor: pointer;
}
.content {
  list-style-type: none;
  overflow: scroll;
  height: 180px;
}
.new-item {
  padding: 15px;
  border-bottom: 1px solid #aaaaaa;
}
.content input {
  width: auto;
}
.content-foot input {
  width: auto;
  margin-right: 5px;
}
.content-foot {
  margin-top: 15px;
  display: flex;
  justify-content: space-between;
}
.undone {
  display: flex;
}
.item-content {
  display: flex;
}
.item-content input {
  margin: 7px 10px 0px 0px;
}
.spacer {
  flex-grow: 1;
}
.remove-item {
  cursor: pointer;
  &:hover {
    color: black;
  }
}
.item-left {
  display: flex;
  align-items: center;
}
.item-label {
  border: 1px solid white;
  margin: 0px;
}
.item-edit {
  font-size: 15px;
  color: #2c3e50;
  width: 100%;
  border: 1px solid #ccc;

  &:focus {
    outline: none;
  }
}
.completed {
  text-decoration: line-through;
  color: grey;
}

#content-foot {
  display: flex;
}
</style>ㄈ
