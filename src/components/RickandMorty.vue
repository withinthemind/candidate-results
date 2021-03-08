<template>
	<div class="container">
		<h1>Rick and Morty</h1>
		<div class="filters">
			<h2>Filters</h2>
			<div class="filter">
			Name: <input type="text" id="name" ref="name" /> Gender: <select id="select" ref="gender"><option value="Unknown">Unknown</option><option value="Male">Male</option><option value="Female">Female</option><option value="genderless">Genderless</option></select> <input type="submit" value="Go" @click="filterByNameGender" />
			</div>
		</div>
		<div v-for="(character) in characters" :key="character.id">
			<div class="card">
				<img :src="character.image" :alt="character.name" />
				<h2>Name: {{ character.name }}</h2>
				<h3>Species: {{ character.species }}</h3>
				<h3>Gender: {{ character.gender }}</h3>
				<h3>Type: {{ character.type ? character.type : 'unknown' }}</h3>
				<h3>Origin: {{ character.origin.name }}</h3>
			</div>
		</div>
		<div class="clear"></div>
		<div class="pager">
			<button @click="prevPage">Previous</button> 
			<button @click="nextPage" :class="{'disabled': index === totalPages}">Next</button>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
export default {
data: () => ({
			api: 'https://rickandmortyapi.com/api/character',
            characters: [],
            pages: [],
			totalPages: 0,
            index: 0,
            prev: '',
            next: '',
			name: '',
			gender: ''
            }),
mounted() {
	let urlParams = new URLSearchParams(window.location.search);
	this.$refs.name.value = urlParams.get('name');
	this.$refs.gender.value = urlParams.get('gender');
	if (this.$refs.name.value || this.$refs.gender.value) {
		this.filterByNameGender();
	}
	else {
		this.getCharacters(this.api);
	}	
},
methods: {
  async getCharacters(url) {
    let config = {
    headers: {
    'Accept': 'application/json'
		}
    }
    axios.get(url, config)
      .then(res => {
      this.characters = res.data.results;
      this.pages[this.index] = res.data.results;
      this.prev = res.data.info.prev;
      this.next = res.data.info.next;
this.totalPages = res.data.info.pages;
      })
      .catch(err => {
        alert(err);
    });
    },
    prevPage() {
      if(this.index > 0){
          this.index--;
      if(this.pages[this.index] == undefined) {
          this.getCharacters(this.prev);
      }else{
      this.characters = this.pages[this.index];
          }
      }
    },
    nextPage() {
      if(this.index < this.totalPages){
          this.index++;
      if(this.pages[this.index] == undefined) {
          this.getCharacters(this.next);
      }else{
      this.characters = this.pages[this.index];
          }
      }
    },
	filterByNameGender() {
		this.name = this.$refs.name.value;
		this.gender = this.$refs.gender.value;
		this.getCharacters(this.api + '/?name=' + this.name + '&gender=' + this.gender);
		window.history.pushState('filters', 'rickandmorty', '/characters?name=' + this.name + '&gender=' + this.gender);
	}	
  }
}
</script>

<style scoped>
.clear {
  clear: both;
}
.container {
	width:1024px;
	margin:auto;
}
.card {
	width:32%;
	float:left;
	height:600px;
}
.pager {
	margin-top:50px;
}
.filters {
	margin:25px 0;
}
.filter {
	margin:10px 0;
}
button {
	margin-right:20px;
	margin-bottom:50px;
	padding:20px;
	border:1px solid #ccc;
	background:blue;
	color:#fff;
	font-weight:bold;
	cursor:pointer;
	outline:none;
}
</style>
