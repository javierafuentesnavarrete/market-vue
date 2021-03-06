<template>
  <div>
    <!-- Flexbox -->
    <section>
      <router-link to="/">Back</router-link>
      <div class="row-1">
        <div class="photo-container">
          <img src="../../assets/images/phone.jpg" />
        </div>
        <div class="details">
          <h3 class="title is-3">{{dummyProduct.title}}</h3>
          <h4 class="title is-4">Product price: {{dummyProduct.price}}</h4>
          <button class="button is-fullwidth">Message Seller</button>
        </div>
      </div>
      <div class="comments">
        <h5 class="title is-5">Comments ({{ lengthOfComments }})</h5>
        <comment
          class="comment"
          v-for="(comment, index) in comments"
          v-bind:key="index"
          :comment="comment"
        />
        <form v-if="loggedIn" v-on:submit.prevent="checkForm">
          <!-- Input -->
          <article class="media">
            <figure class="media-left">
              <p class="image is-64x64">
                <img src="https://bulma.io/images/placeholders/128x128.png" />
              </p>
            </figure>
            <div class="media-right">
              <textarea
                @keydown="errors = []"
                v-model="comment.body"
                class="textarea"
                placeholder="Add a comment..."
              ></textarea>
              <!-- Error  Handling -->
              <div class="comment-valid">
                <div class="comment-valid__text" v-if="errors.length">
                  <ul v-for="(error, index) in errors" v-bind:key="index">
                    <li class="error">{{ error }}</li>
                  </ul>
                </div>
                <div class="comment-valid__text" v-if="!errors.length">{{maxLetters - comment.body.length}} characters remaining</div>
                <!-- Submit -->
                <input type="submit" class="button is-primary" value="Post Comment" />
              </div>
            </div>
          </article>
        </form>
      </div>
    </section>
  </div>
</template>

<script>
import Comment from "../comment/Comment";

export default {
  name: "ProductDetail",
  components: {
    comment: Comment
  },

  data: function() {
    return {
      readOnly: false,
      maxLetters: 100,
      errors: [],
      loggedIn: "",
      product: {},
      image: [],
      comments: [],
      comment: {
        body: "",
        product: null,
        user: null
      },
      dummyProduct: {
        title: "Samsung Galaxy S10+ 128GB G975F Prism Black",
        price: "10,000",
        description: `LEARANCE PRICING!

Condition: New
1-day Warranty: 12 Months
What's in the Box?

    Samsung Galaxy Smartphone
    Charging Plug 

The result of 10 years of pioneering mobile firsts, the next generation of Galaxy has arrived - the phone that doesn't just stand out, it stands apart...

The Most Immersive Display Yet: With on-screen security, and a Dynamic AMOLED that's easy on the eyes, there's virtually nothing to get in the way of your viewing. Not even the screen you're viewing it on.

Ultrasonic Fingerprint Security: We've moved security from the back of the phone to the front, fusing the Ultrasonic Fingerprint directly into the screen.`
      }
    };
  },
  watch: {
    errors: function() {
      if (this.errors.length) {
        document.querySelector("textarea").classList.add("is-danger");
      } else {
        document.querySelector("textarea").classList.remove("is-danger");
      }
    }
  },
  computed: {
    lengthOfComments: function() {
      return this.comments.length;
    },
    letterCount: function() {
      return 100 - this.comment.body.length;
    }
  },
  methods: {
    checkForm: function(event) {
      event.preventDefault();
      this.errors = [];
      if (!this.comment.body) {
        this.errors.push("Comment required");
      }
      // if no errors then log the user in
      if (!this.errors.length) {
        this.postComment(this.comment);
      }
    },
    postComment: function(comment) {
      const id = this.$route.params.productId;
      this.comment.product = id;
      this.comment.user = localStorage.username;
      this.$http
        .post(`${process.env.VUE_APP_API_URL}products/${id}/comments`, comment)
        .then(
          response => {
            if (response.body) {
              this.comment.body = "";
              this.getProductById();
              this.getComments();
            }
          },
          response => {
            this.errors.push(response.body.message);
          }
        );
    },
    getProductById: function() {
      const id = this.$route.params.productId;
      this.$http
        .get(`${process.env.VUE_APP_API_URL}products/${id}`)
        .then(function(data) {
          this.product = data.body;
        });
    },
    getComments: function() {
      const id = this.$route.params.productId;
      this.$http
        .get(`${process.env.VUE_APP_API_URL}products/${id}/comments`)
        .then(function(data) {
          this.comments = data.body;
        });
    }
  },

  created: function() {
    // const id = this.$route.params.articleId;
    (this.product = {}),
      (this.loggedIn = localStorage.loggedIn),
      this.getProductById();
    this.getComments();
  }
};
</script>

<style lang="scss">
@import "../../scss/variables";
@import "../../scss/bulma";

* {
  font-family: Arial, Helvetica, sans-serif;
}

section {
  max-width: 1200px;
  margin: auto;
  padding: 1em;
}

.row-1 {
  @include flex-direction(row);
}

.error {
  font-size: 1em;
  color: #f14668;
}

.photo-container {
  @include flex-direction(row);
  justify-content: center;
  background: $off-white;
  flex: 2;
  & img {
    height: 100%;
    object-fit: cover;
  }
}

.details {
  flex: 1;
  grid-column: 2 / 3;
  padding: 25px;
  & button {
    padding: 25px 0;
  }
}

.comments {
  grid-column: 1 / 2;
  padding: 25px;
}

.comment {
  width: 100%;
}

.comment-valid {
  @include flex-direction(row);
  justify-content: space-between;
  margin-top: 10px;
  &__text {
    margin: 5px 0
  }
  & input[type=submit] {
    margin: 5px 0
  }
}

.media {
  @include flex-direction(row);
  margin-top: 25px;
}

.media-right {
  flex: 1;
}
//Mobile
@media (max-width: 700px) {
  .row-1 {
    flex-direction: column;
  }

  .photo-container {
    grid-column: 1 / 3;
  }

  .comments {
    grid-column: 1 / 3;
    grid-row: 3;
  }

  .details {
    grid-column: 1 / 3;
    grid-row: 2;
  }

  .comment-valid {
    flex-direction: column;
  }
}
</style>
