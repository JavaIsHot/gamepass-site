<script>
	import { onMount } from "svelte";
  
	let images = [];
	let sortedImages = [];
	let searchTerm = "";
	let sortBy = "alphabetical";
  
	onMount(async () => {
	  // Fetch images dynamically from the 'images' folder
	  const imageFiles = import.meta.glob('/images/*.png');
  
	  images = Object.keys(imageFiles).map((key, index) => ({
		id: index + 1,
		name: key.match(/\/(\d+)\.png$/)[1], // Extracting the number from the file path
		path: key,
	  }));
  
	  // Initial sorting
	  sortImages();
	});
  
	function sortImages() {
	  sortedImages = [...images];
  
	  if (sortBy === "alphabetical") {
		sortedImages.sort((a, b) => a.name.localeCompare(b.name));
	  } else if (sortBy === "numerical") {
		sortedImages.sort((a, b) => a.id - b.id);
	  } else if (sortBy === "mostDownloaded") {
		// You may need to fetch download counts from the server and sort accordingly
	  }
	}
  
	function downloadImage(image) {
	  // In a real-world scenario, you would send a request to the server to update the download count
	  // For now, we'll just log to the console
	  console.log(`Downloading ${image.name}`);
	}
  </script>
  
  <style>
	.grid {
	  display: grid;
	  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
	  gap: 16px;
	}
  
	.image {
	  width: 100%;
	  height: 200px;
	  object-fit: cover;
	}
  </style>
  
  <div>
	<div>
	  <label for="search">Search:</label>
	  <input type="text" id="search" bind:value={searchTerm} />
	</div>
  
	<div>
	  <label for="sort">Sort by:</label>
	  <select id="sort" bind:value={sortBy} on:change={sortImages}>
		<option value="alphabetical">Alphabetical</option>
		<option value="numerical">Numerical</option>
		<option value="mostDownloaded">Most Downloaded</option>
	  </select>
	</div>
  
	<div class="grid">
	  {#each sortedImages.filter(img => img.name.includes(searchTerm.toLowerCase())) as image (image.id)}
		<div class="image" on:click={() => downloadImage(image)}>
		  <img src={image.path} alt={image.name} />
		  <p>{image.name}</p>
		</div>
	  {/each}
	</div>
  </div>
  