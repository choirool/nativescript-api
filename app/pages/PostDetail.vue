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
        <Label :text="post.body" textWrap="true" verticalAlignment="top" />
        <StackLayout class="hr m-10"></StackLayout>
      </StackLayout>
      <StackLayout row="1" orientation="vertical">
        <Label text="Comments" row="0" />
        <GridLayout rows="210, auto">
          <RadListView ref="postCommentList" for="item in comments" row="0">
            <v-template>
              <StackLayout class="item" orientation="vertical">
                <Label :text="item.email" class="post-title"></Label>
                <Label :text="item.body" class="post-body"></Label>
              </StackLayout>
            </v-template>
          </RadListView>
          <StackLayout row="1" v-if="!hideCommentForm">
            <RadDataForm
              ref="formComment"
              :source="commentData"
              :metadata="commentMetadata"
            >
            </RadDataForm>
            <Button text="Button" @tap="submitComment" row="2" />
          </StackLayout>
        </GridLayout>
      </StackLayout>
    </GridLayout>
  </Page>
</template>

<script>
import { DataFormValidationMode } from "nativescript-ui-dataform";

export default {
  props: ["post"],
  data() {
    return {
      comments: [],
      loadingComment: false,
      hideCommentForm: false,
      validationMode: DataFormValidationMode.Immediate,
      commentData: {
        name: "",
        email: "",
        comment: "",
      },
      commentMetadata: {
        isReadOnly: false,
        commitMode: "Immediate",
        validationMode: "immediate",
        propertyAnnotations: [
          {
            name: "name",
            displayName: "Name",
            validators: [
              {
                name: "NonEmpty",
                params: { errorMessage: "This field is required" },
              },
              { name: "MaximumLength", params: { length: 10 } },
            ],
          },
          {
            name: "email",
            displayName: "Email address",
            editor: "Email",
            validators: [
              {
                name: "NonEmpty",
              },
            ],
          },
          {
            name: "comment",
            displayName: "Comment",
            validators: [
              {
                name: "NonEmpty",
              },
            ],
          },
        ],
      },
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
    submitComment() {
      this.$refs.formComment.validateAll().then((result) => {
        if (result) {
          this.$refs.formComment.commitAll();
          this.postComment();
        }
      });
    },
    postComment() {
      let data = JSON.stringify({
        postId: this.post.id,
        name: this.commentData.name,
        email: this.commentData.email,
        body: this.commentData.comment,
      });

      fetch("http://10.0.2.2:3000/comments", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: data,
      })
        .then((r) => r.json())
        .then((response) => {
          this.hideCommentForm = true;
          alert({
            title: "Successful",
            message: "Comment success",
            okButtonText: "OK",
          });
        })
        .catch((e) => {
          console.log(e);
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