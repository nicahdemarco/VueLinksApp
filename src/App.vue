<template>
	<div id="app" class="container">
		<h1>Vue con Firebase</h1>

		<div class="card">
			<div class="card-header">
				<h3>Add a link</h3>
			</div>

			<div class="card-body">
				<form v-on:submit.prevent="addLink" class="form-inline">
					<div class="form-group">
						<label>Title</label>
						<input v-model="newLink.title" class="form-control mx-sm-4" placeholder="Title" type="text" />
					</div>

					<div class="form-group">
						<label>Author</label>
						<input v-model="newLink.author" class="form-control mx-sm-4" placeholder="Author" type="text" />
					</div>

					<div class="form-group">
						<label>URL</label>
						<input v-model="newLink.url" class="form-control mx-sm-4" placeholder="URL" type="text" />
					</div>

					<input type="submit" value="Add link" class="btn btn-success" />
				</form>
			</div>

			<div class="card">
				<div class="card-header">
					<h3 class="card-title">Link list</h3>
					<table class="table table-striped">
						<thead>
							<tr>
								<th>Title</th>
								<th>Author</th>
								<th>Delete</th>
							</tr>
						</thead>
						<tbody>
							<tr v-for="(link, index) in links" :key="index">
								<!-- <tr v-for="link in links"> -->
								<td>
									<a v-bind:href="link.url">{{ link.title }}</a>
								</td>
								<td>{{ link.author }}</td>
								<td>
									<button v-on:click="deleteLink(link)" class="btn btn-danger">
										<i class="fa fa-trash-o" aria-hidden="true"></i>
									</button>
								</td>
							</tr>
						</tbody>
					</table>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import Firebase, { functions } from 'firebase';
import toastr from 'toastr';

let config = {
	apiKey: 'AIzaSyB-bbs4cKwkfHPS0GCZPavce8BXSBEBc5A',
	authDomain: 'vuejsfire-fb7ea.firebaseapp.com',
	databaseURL: 'https://vuejsfire-fb7ea.firebaseio.com',
	projectId: 'vuejsfire-fb7ea',
	storageBucket: '',
	messagingSenderId: '1059288466143',
	appId: '1:1059288466143:web:f04bc51e875e4955',
};

let app = Firebase.initializeApp(config);
let db = app.database();

let linksRef = db.ref('links');

export default {
	name: 'app',

	firebase: {
		links: linksRef,
	},

	data() {
		return {
			newLink: {
				title: '',
				author: '',
				url: '',
			},
			// links: ['links'],
		};
	},

	methods: {
		addLink: function() {
			linksRef.push(this.newLink);
			this.newLink.title = '';
			this.newLink.author = '';
			this.newLink.url = '';
		},

		deleteLink: function(link) {
			linksRef.child(link['.key']).remove();
			toastr.success('Link removed');
		},
	},
};
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}

.form-control {
	padding: 1%;
	margin-right: 1%;
}
</style>
