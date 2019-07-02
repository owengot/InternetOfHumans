<template>
  <div>
    <div class="post_viewer">
      <div v-if="!current || current.data.title == 'Internet of Humans'" class="postsByTag">
        <div class="nav_header">{{ posts.length }} Topics by {{ users.length }} users</div>
        <div class="allposts">
          <div v-for="post in posts" class="postEntry">
            <a :href="'https://edgeryders.eu/t/' + post.slug" target="_blank">{{ post.title }}</a>
            <div class="post_meta">
              <p>{{ post.views }} Views</p>
              <p>{{ post.posts_count }} Replies</p>
              <p>
                Posted by {{ getUser(post.posters[0].user_id) }} on
                {{ post.created_at | formatDate }}
              </p>
            </div>
            <div
              v-html="post.excerpt.replace(/(<([^>]+)>)/gi, '').replace(/\s*\[.*?\]\s*/g, '')"
              class="excerpt"
            ></div>

            <div v-if="post.tags.length" class="tags">
              <p v-for="tag in post.tags" class="tag">#{{ tag }}</p>
            </div>
          </div>
        </div>
      </div>

      <div v-else class="postsByTag">
        <div v-if="current.data.views" class="post">
          <a :href="'https://edgeryders.eu/t/' + currentNode.slug" target="_blank">{{
            currentNode.title
          }}</a>
          <div class="post_meta">
            <p>{{ currentNode.views }} Views</p>
            <p>{{ currentNode.posts_count }} Replies</p>
            <p>
              Posted by {{ getUser(currentNode.posters[0].user_id) }} on
              {{ currentNode.created_at | formatDate }}
            </p>
          </div>

          <div v-html="getPost(currentNode.id).postexcerpt" class="excerpt"></div>
        </div>
        <div v-else class="postsByTag">
          <div class="nav_header">
            {{ currentNode.length }} Topics tagged #{{ currentNode[0].tag }}
          </div>
          <div class="allposts">
            <div v-for="(post, index) in currentNode" class="postEntry">
              <a :href="'https://edgeryders.eu/t/' + getPost(post.id).slug" target="_blank">{{
                getPost(post.id).title
              }}</a>

              <div class="post_meta">
                <p>{{ getPost(post.id).views }} Views</p>
                <p>{{ getPost(post.id).posts_count }} Replies</p>
                <p>
                  Posted by {{ getUser(post.posters[0].user_id) }} on
                  {{ getPost(post.id).created_at | formatDate }}
                </p>
              </div>

              <div
                v-html="post.postexcerpt.replace(/(<([^>]+)>)/gi, '').replace(/\s*\[.*?\]\s*/g, '')"
                class="excerpt"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
/**
 * Component that display the percentage value of the current node relative to root.
 * Can be used as a "top" slot of sunburst component.
 */
import moment from "moment"

export default {
  name: "nodeInfoDisplayer",
  data() {
    return {
      activePost: null,
      openPost: false,
      base: true,
    }
  },
  props: {
    /**
     *  Root node
     */
    root: {
      required: false,
      type: Object,
    },
    /**
     *  Current node
     */
    current: {
      required: false,
      type: Object,
    },
    /**
     *  Clicked node
     */
    clicked: {
      required: false,
      type: Object,
    },
    /**
     *  Show fraction format of size if true
     */
    posts: {
      required: false,
      type: Array,
    },

    users: {
      required: false,
      type: Array,
    },
  },
  computed: {
    currentNode() {
      if (this.current.data.children) {
        return this.current.data.children
      } else {
        const singlePost = this.posts.find(post => post.id === this.current.data.id)
        return singlePost
      }
    },
    isCurrent() {
      if (this.current.data.children.find(post => post.id === this.activePost)) {
        return true
      } else {
        return false
      }
    },
    currentPost() {
      if (this.current.data.children) {
        return this.current.data.children[0]
      } else {
        return null
      }
    },
  },
  methods: {
    getPost(idvalue) {
      return this.posts.find(post => post.id === idvalue)
    },
    getUser(userid) {
      let userP = this.users.find(user => user.id === userid)

      if (userP) {
        return userP.username
      } else {
        return null
      }
    },
    getAuthor(postid) {
      let selectedpost = this.posts.find(post => post.id === postid)
      let authorId = selectedpost.posters[0].user_id
      let authorProfile = this.users.find(user => user.id === authorId)

      var authorName = null

      if (authorProfile.name) {
        authorName = authorProfile.name
      } else {
        authorName = authorProfile.username
      }

      const obj = {
        avatar: "https://edgeryders.eu" + authorProfile.avatar_template.replace("{size}", "200"),
        name: authorName,
      }

      return obj
    },
    slideMe(value) {
      this.activePost = value
    },
  },
  filters: {
    formatDate: function(value) {
      return moment(String(value)).format("MM/DD/YYYY hh:mm")
    },
  },
}
</script>
<style lang="scss" scoped>
.post {
  padding: 0 0px;
  background: #fff;

  a {
    background: rgba(0, 0, 0, 0.08);
    padding: 15px 20px;
    color: black !important;
    margin: 0 0 20px !important;
    &:after {
      background: url("data:image/svg+xml,%3Csvg aria-hidden='true' data-prefix='fas' data-icon='chevron-circle-right' class='svg-inline--fa fa-chevron-circle-right fa-w-16' xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Cpath fill='%23000' d='M256 8a248 248 0 1 1 0 496 248 248 0 0 1 0-496zm114 231L234 104c-9-10-24-10-33 0l-17 17c-10 9-10 24 0 33l101 102-101 102c-10 9-10 24 0 34l17 17c9 9 24 9 33 0l136-136c9-9 9-25 0-34z'/%3E%3C/svg%3E") !important;
      background-repeat: no-repeat;
      content: "";
      width: 13px;
      height: 13px;
      display: inline-block;
      position: relative;
      left: 8px;
      top: 1.2px;
    }
  }

  .excerpt {
    margin: 10px 0px !important;
    padding: 0 20px;
  }
}

.postsIndex {
  width: 100%;
  height: 86%;
  overflow-x: hidden;
  overflow-y: scroll !important;
}

h4 {
  margin: 20px 20px 10px;
}

.allposts {
  height: 86%;
  overflow-y: scroll;
  overflow-x: hidden;
  width: 100%;
}

.nav_header {
  background: rgba(0, 0, 0, 0.05);
  padding: 13px 20px;
  color: black;
  font-weight: bold;
}

.postExcerpt {
  position: absolute;
  right: 0px;
  width: 90%;
  overflow: scroll;
  height: 500px;
  transform: translateX(100%);
  transition: all 0.9s cubic-bezier(0.075, 0.82, 0.165, 1);
  &.active {
    transform: translateX(0%);
    padding: 0 20px;
  }
  &.notactive {
    transform: translateX(100%);
  }
  h4 {
    border-bottom: 1px solid rgba(0, 0, 0, 0.05);
    padding: 0 0 10px 0;
    margin: 14px 0 10px 0 !important;
  }
}

.avatar {
  border-radius: 100px;
  float: left;
  width: 25px;
  height: 25px;
}

.tags {
  display: inline-block;
  width: 100%;
  margin: 0 0 0 20px;
}
.tag {
  border-radius: 0px;
  padding: 6px 10px;
  display: inline-block;
  font-size: 14px;
  margin: 0 10px 10px 0px;
  float: left;
  background: blue;
  color: white;
}

.excerpt {
  padding: 10px 20px;
}

.post_meta {
  display: inline-flex;
  width: auto;
  width: 90%;
  margin: 0px 3.5% 4px;
  border-radius: 3px;
  background: white;
  p {
    padding: 10px 12px 8px;
    margin: 0 3px 0 0;
    background: blue;
    color: #fff;
    font-size: 14px;
    &:last-child {
      border-right: none;
    }
  }
}
.user,
.replies,
.viewPost {
  padding: 0 12px 0 0;
  border-radius: 4px;
  font-size: 14px;
  overflow: hidden;
  height: 40px;
  align-items: center;
  img {
    width: auto;
    height: 92%;
    border-radius: 3px;
    margin: 0 10px 0 2px;
  }
  font-weight: bold;
  border: 2px solid rgba(0, 0, 0, 0.1);
  display: inline-flex;
  margin: 0px 0 10px 0;
  float: left;
}

.postInfo {
  margin: 0px 0 12px 2px;
  font-weight: bold;
}
.back {
  border-radius: 4px;
  display: inline-block;
  padding: 8px 18px 8px !important;
  float: none !important;
  clear: both;
  margin: 20px 0 12px 0;
  background: #000;
  color: white;
  font-weight: bold;
  font-size: 14px;
  &:hover {
    cursor: pointer;
  }
}
.user {
  background: #fff;
  color: #000 !important;
}
.replies {
  background: none;
  color: #000;
  padding: 0 10px;
  margin: 0 10px;
}

.viewPost {
  background: orangered !important;
  border: 2px solid orangered;
  color: white;
  text-decoration: none;
  text-align: center;
  padding: 0 10px;
}

.postsByTag {
  width: 100%;
  margin: 0;
  background: white;
  overflow: hidden;
  padding: 0px 0px;
  position: relative;
  height: 100%;
  left: 0px;
  h4 {
    margin: 20px 20px 10px;
  }
}
.postTitle {
  color: black;
  margin: 0 0px;
  text-align: left;
  position: relative;
  z-index: 999999;
  width: 100%;
  display: block;

  margin: 0;
  padding: 6px 10px 6px 18px !important;
  position: relative;
  z-index: 999999;
}

.postTitle.active {
  background: rgba(0, 0, 0, 1) !important;
  color: white;
  position: relative;

  //         &:after, &:before{
  //     content:"";
  //     position:absolute;
  //     width:20px;
  //     height:50%;
  //     left:100%;
  //     z-index: 999999 !important;
  // }
  // &:after{
  //     bottom:0;
  //     background: linear-gradient(to right bottom, rgba(0,0,0,0.05) 50%, transparent 50%);
  // }
  // &:before{
  //       top:0;
  //     background: linear-gradient(to right top, rgba(0,0,0,0.05) 50%, transparent 50%);
  // }
}

.postEntry,
.post {
  width: 100%;
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
  a {
    font-weight: bold;
    text-decoration: none;
    color: black;
    margin: 20px 20px 10px;
    display: block;
    position: relative;
    &:after {
      background: url("data:image/svg+xml,%3Csvg aria-hidden='true' data-prefix='fas' data-icon='chevron-circle-right' class='svg-inline--fa fa-chevron-circle-right fa-w-16' xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Cpath fill='currentColor' d='M256 8a248 248 0 1 1 0 496 248 248 0 0 1 0-496zm114 231L234 104c-9-10-24-10-33 0l-17 17c-10 9-10 24 0 33l101 102-101 102c-10 9-10 24 0 34l17 17c9 9 24 9 33 0l136-136c9-9 9-25 0-34z'/%3E%3C/svg%3E");
      background-repeat: no-repeat;
      content: "";
      width: 13px;
      height: 13px;
      display: inline-block;
      position: relative;
      left: 8px;
      top: 1.2px;
    }
  }
}

.postEntry:hover {
  cursor: pointer;
}
.post_viewer {
  width: 57%;
  height: 77%;
  border: 1px solid rgba(0, 0, 0, 0.05);
  border-radius: 10px;
  position: absolute;
  overflow: hidden;
  top: 15%;
  padding: 0;
  left: 43%;
  z-index: 99;
  span {
    font-size: 2em;
  }
  span.detail {
    font-size: 1em;
  }
}
</style>
