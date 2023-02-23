# Quasar/Vuejs/Firebase

### General

#### Update to latest quasar version:

```text
npm i -g @quasar/cli@latest
```

### Lessons Learned

The best approach is to use Firestore directly in the app without a nodejs backend for most things. Vuexfire seems to be a good candidate to abstract a lot of hassle away for this.

#### Backend Query from .vue File:

```javascript
methods: {
    getPosts() {     
      this.$axios
        .get(`${process.env.API}/posts`, {
          headers: { Authorization: this.$q.localStorage.getItem("token") },
        })
        .then((resp) => {
          this.posts = resp.data;
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status == 403) {
              this.$q.localStorage.remove("loggedIn");
              this.$q.localStorage.remove("token");
              this.$router.push("/auth").catch((err) => {console.log(err)});
            }
          }
        });
    },
```

When commiting to git, include `.postcssrc.js` manually or the quasar app will break.

#### JS Promises

A more elegant way to write asynchronous functions without using .then and .catch is this format:

```javascript
const funcName = async() => {
    const a = getA()
    const b = getB()
    const c = await Promise.all([a,b])
    return c
}
```

You could also await getA\(\) and getB\(\) separately, but they would not execute in parallel like in the example. This is useful if the value of B depends on A.

A good explanation can be found here: [https://www.youtube.com/watch?v=vn3tm0quoqE](https://www.youtube.com/watch?v=vn3tm0quoqE)

