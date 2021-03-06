<template>
  <Page class="page">
    <ActionBar class="action-bar">
      <NavigationButton
        ios:visibility="collapsed"
        icon.decode="font://&#xf060;"
        class="fas action-icon"
        @tap="$navigateBack({}, null)"
      />
      <ActionItem
        icon.decode="font://&#xf060;"
        class="fas action-icon"
        android:visibility="collapsed"
        ios.position="left"
        @tap="$navigateBack({}, null)"
      />
      <Label class="action-bar-title" :text="post.title" />
    </ActionBar>
    <GridLayout rows="auto, auto">
      <StackLayout row="0">
        <Label
          :text="post.body"
          textWrap="true"
          verticalAlignment="top"
          class="post-body"
        />
      </StackLayout>
      <StackLayout row="1">
        <Label text="Comments" row="0" />
        <GridLayout rows="*">
          <RadListView ref="postCommentList" for="item in comments">
            <v-template>
              <StackLayout class="item" orientation="vertical">
                <Label :text="item.email" class="post-title"></Label>
                <Label :text="item.body" class="post-body"></Label>
              </StackLayout>
            </v-template>
          </RadListView>
        </GridLayout>
      </StackLayout>
    </GridLayout>
  </Page>
</template>

<script>
export default {
  props: ["post"],
  data() {
    return {
      comments: [],
      loadingComment: false,
    };
  },
  mounted() {
    console.log(this.post);
  },
  created() {
    this.fetchComments();
  },
  methods: {
    async fetchComments() {
      await fetch(`http://10.0.2.2:3000/comments?postId=${this.post.id}`)
        .then((response) => response.json())
        .then((r) => {
          r.forEach((comment) => {
            this.comments.push(comment);
          });
          this.loadingComment = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style scoped lang="scss">
// Start custom common variables
@import "~@nativescript/theme/scss/variables/blue";

.action-icon {
  font-size: 20;
}

.post-body {
  border-bottom-color: black;
  border-bottom-style: solid;
  border-bottom-width: 1;
}
</style