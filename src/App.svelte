<script>
  import Message from "./Message.svelte";
  import CreateMessage from "./CreateMessage.svelte";
  import Login from "./Login.svelte";
  import Logout from "./Logout.svelte";

  import firebase from "firebase/app";
  import "firebase/auth";
  import "firebase/firestore";
  import { firebaseConfig } from "./firebaseConfig.js";
  import { authState } from "rxfire/auth";
  import { first, startWith, tap } from "rxjs/operators";
  import { collectionData } from "rxfire/firestore";
	import axios from "axios";

  firebase.initializeApp(firebaseConfig);

  const db = firebase.firestore();
  const auth = firebase.auth();
  const googleProvider = new firebase.auth.GoogleAuthProvider();

  let user = authState(auth);
  console.log(auth, '---------------------')

  const login = () => {
    auth.signInWithRedirect(googleProvider);
	};

  const logout = () => {
    auth.signOut()
	};

  const sendMessage = ev => {
    console.log(ev);
    const apiUrl = __myapp.env.ALGOLIA_KEY

		const headers = {
		"Content-Type": "application/json",
		"Authorization": "Bearer " + apiUrl
		}

		const question = "I am a highly intelligent Ai that can generate correct html from any text. this html is intended to be renderer by any browser. \ninput: the word google. \noutput:  <html> <body> <a> Google </a> </body> </html> \ninput: the word cat. \noutput:  <html> <body> <h1> cat </h1> </body> </html> \ninput: google logo at the center, search bar at the bottom of logo with button to the right that say search now. \noutput:  <html> <body> <h1> logo search bar </h1> <img src=\"http://www.google.com/images/srpr/logo11w.png\" width=\"332\" height=\"58\" alt=\"google logo\" > <form> <input type=\"text\" name=\"q\"> <input type=\"submit\"> </form> </body> </html> \ninput: " + ev.detail + "\noutput:"
		axios.post('https://api.openai.com/v1/engines/davinci/completions', {
				prompt: question,
				"temperature": 0,
				"max_tokens": 100,
				"top_p": 1,
				"frequency_penalty": 0,
				"presence_penalty": 0,
				"stop": ["</html>"]
		},{
		headers: headers
		}).then(
				(response) => {
					console.log(response.data.choices[0])
					return user.pipe(first()).subscribe(u =>
						db
							.collection("messages")
							.doc(new Date().getTime().toString())
							.set({
								displayName: 'Ai',
								photoURL: 'https://blog.desdelinux.net/wp-content/uploads/2019/09/openai-proyectos-inteligencia-artificial-imagen-introduccion-blog-desdelinux.jpg.webp',
								text: response.data.choices[0].text + "</html>"
							})
    );
				}
		).catch((error) => {console.log(error)})

    return user.pipe(first()).subscribe(u =>
      db
        .collection("messages")
        .doc(new Date().getTime().toString())
        .set({
          displayName: u.displayName,
          photoURL: u.photoURL,
          text: ev.detail
        })
    );
  };

  let messages = collectionData(db.collection("messages"), "id").pipe(
    startWith([]),
    tap(_ => setTimeout(_ => window.scrollTo(0,document.body.scrollHeight), 500))
  );
</script>

<style>
  .section {
    height: 100%;
  }
  .card {
    height: 70%;
    overflow-y: scroll;
  }
</style>

<svelte:head>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.7.5/css/bulma.min.css" />
</svelte:head>

<div class="section">
  <div class="card">
    <div class="card-content">
    {#each $messages as message}
      <Message {...message} />
    {/each}
  </div>
</div>

  {#if $user}
    <CreateMessage on:send={sendMessage} />
    <Logout on:logout={logout} />

  {:else}
    <Login on:login={login} />
  {/if}
</div>
