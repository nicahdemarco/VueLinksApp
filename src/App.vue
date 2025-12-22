<template>
	<div id="app" class="container">
		<h1>Vue con Firebase</h1>

		<div class="card">
			<div class="card-header">
				<h3>Add a link</h3>
			</div>

			<div class="card-body add-link-section">
				<form @submit.prevent="addLink" class="form-inline">
					<div class="form-group">
						<label for="title">Title</label>
						<input v-model="newLink.title" id="title" class="form-control mx-sm-4" placeholder="exampleLink"
							type="text" />
					</div>

					<div class="form-group">
						<label for="author">Author</label>
						<input v-model="newLink.author" id="author" class="form-control mx-sm-4" placeholder="Author"
							type="text" />
					</div>

					<div class="form-group">
						<label for="url">URL</label>
						<input v-model="newLink.url" id="url" type="url" class="form-control mx-sm-4"
							placeholder="https://example.com" />
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
							<tr v-for="(link, idx) in linksList" :key="idx">
								<td>
									<a :href="link.url" :title="link.url" target="_blank" rel="noopener noreferrer"> {{
										link.title }} </a>
								</td>
								<td>{{ link.author }}</td>
								<td>
									<button @click="deleteLink(idx)" class="btn btn-danger">
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
import firebase from 'firebase/app';
import 'firebase/database';
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

let app = firebase.initializeApp(config);
let db = app.database();
let linksRef = db.ref('links');

export default {
	name: 'app',

	data() {
		return {
			newLink: {
				title: '',
				author: '',
				url: '',
			},
			linksList: JSON.parse(localStorage.getItem('links')) || [],
		};
	},

	created() {
		linksRef.on('value', snapshot => {
			let data = snapshot.val() || {};
			let arr = [];

			for (let key in data) {
				arr.push({
					key,
					title: data[key].title,
					author: data[key].author,
					url: data[key].url,
				});
			}

			this.linksList = arr;
			localStorage.setItem('links', JSON.stringify(arr));
		});
	},

	methods: {
		addLink() {
			if (!this.newLink.title || !this.newLink.author || !this.newLink.url) {
				toastr.error('Please fill in all fields');
				return;
			}

			linksRef.push({
				title: this.newLink.title,
				author: this.newLink.author,
				url: this.newLink.url,
			});

			this.cleanInputs();
			toastr.success('Link added');
		},

		deleteLink(idx) {
			let item = this.linksList[idx];
			linksRef.child(item.key).remove();

			toastr.success('Link removed');
		},

		cleanInputs() {
			this.newLink = { title: '', author: '', url: '' };
		},
	},
};
</script>

<style lang="css">
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

.add-link-section {
	background: #fff;
	padding: 2rem;
	border-radius: 12px;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
	max-width: 900px;
	margin: 0 auto 2rem auto;
}

.add-link-section h2 {
	font-size: 1.8rem;
	color: #16a34a;
	margin-bottom: 1.5rem;
	text-align: center;
}

body {
	background: #f3f4f6;
	background: linear-gradient(135deg, #41B883 0%, #34495E 100%);
	min-height: 100vh;
	font-family: "Inter", sans-serif;
	color: #333;
	padding: 2rem;
}

#app {
	max-width: 900px;
	margin: 0 auto;
}

h1 {
	text-align: center;
	font-size: 2.2rem;
	font-weight: 700;
	color: #0d19c5a2;
	margin-bottom: 2rem;
}

.card {
	background: #fff;
	border-radius: 10px;
	padding: 1.8rem;
	margin-bottom: 2rem;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.card-header h3 {
	font-size: 1.3rem;
	font-weight: 600;
	color: #374151;
	margin-bottom: 1rem;
}

form {
	display: grid;
	grid-template-columns: 1fr;
	gap: 1rem;
}

@media (min-width: 768px) {
	form {
		grid-template-columns: repeat(3, 1fr);
	}
}

form label {
	font-size: 0.9rem;
	font-weight: 500;
	color: #4b5563;
	margin-bottom: 0.3rem;
	display: block;
}

form input[type="text"],
form input[type="url"] {
	width: 100%;
	padding: 0.6rem 0.8rem;
	border: 1px solid #d1d5db;
	border-radius: 6px;
	font-size: 0.95rem;
	transition: 0.2s ease;
}

form input:focus {
	border-color: #16a34a;
	outline: none;
	box-shadow: 0 0 0 3px rgba(22, 163, 74, 0.25);
}

form input[type="submit"] {
	grid-column: span 3;
	background: #16a34a;
	color: white;
	padding: 0.7rem 1rem;
	border: none;
	border-radius: 6px;
	font-size: 1rem;
	font-weight: 600;
	cursor: pointer;
	transition: background 0.2s ease;
}

form input[type="submit"]:hover {
	background: #15803d;
}

table {
	width: 100%;
	border-collapse: collapse;
	margin-top: 1rem;
}

thead {
	background: #dcfce7;
}

thead th {
	text-align: left;
	padding: 0.8rem;
	font-size: 0.95rem;
	color: #374151;
	font-weight: 600;
}

tbody tr {
	border-bottom: 1px solid #e5e7eb;
	transition: background 0.2s ease;
}

tbody tr:hover {
	background: #f9fafb;
}

tbody td {
	padding: 0.8rem;
	font-size: 0.95rem;
}

tbody a {
	color: #16a34a;
	font-weight: 500;
	text-decoration: none;
}

tbody a:hover {
	text-decoration: underline;
}

.btn-danger {
	background: transparent;
	border: none;
	cursor: pointer;
	color: #dc2626;
	font-size: 1.2rem;
	transition: color 0.2s ease;
}

.btn-danger:hover {
	color: #f0f0f0;
}
</style>
