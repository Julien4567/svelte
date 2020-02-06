<script>
  import DB from "./DB.svelte";
  import "@polymer/paper-card";
  import "@material/mwc-button";
  import "@material/mwc-top-app-bar";
  import "@material/mwc-icon-button";
  let books = [];
  let visibility = "true";
  let data = {
    title: "",
    img: "",
    author: "",
    price: "",
    id: null
  };
  
  function onDelete(){
    let id= this.parentNode.parentNode.parentNode.parentNode.parentNode.parentNode.id;
    console.log(id);
    if (confirm("Etes vous cerrtains de vouloir supprimer ce livre ?")){
      DB.get(id).then(function(doc){
        document.getElementById(id).remove();
        return db.remove(doc);
      })
    }
  }

  function modify(id){
    return id;
  }

  function addBook(){
    
    let titre = document.getElementById("titre").value;
    let id = titre;
    let author = document.getElementById("auteur").value;
    let price = document.getElementById("prix").value;
    let url = document.getElementById("url").value;
    let img = "";

    if (document.getElementbyId("image").files && document.getElementById("image").files[0]){
      var fr = new FileReader();
      fr.addEventListener("load", function(e){
        img=e.target.result.split(',')[1];
        DB.post({
          title : titre,
          id: id,
          author: author,
          price: price,
          url: url,
          img: {data: img}
        }).then(function(response){
            console.log("livre ajouté : "+ response);
        }).catch(function(err){
          console.log("Erreur lors de l'ajout du livre");
        });
      
      let cardwrapper = document.getElementById("card-wrapper");
      let newcard = `
      <paper-card heading={book.title}>
	      <img src="data:image/png;base64, {book.img.data}" alt="{book.title}" title="{book.title}"> 	
        <div class="card-content">
          Ecrit par {book.author} et vendu à seulement {book.price}€.
        </div>
        <div>
          <button on:click={modify}>Modifier</button>
          <button on:click={book => onDelete(book)}>Supprimer</button>
        </div>
      </paper-card>
      `;
      cardwrapper.insertAdjacentHTML("beforeend", newcard);
      });
      fr.readAsDataURL(document.getElementById('addImage').files[0]);
    }
    else{
      M.toast({html: "Remplissez tous les champs."});
    }
  }
  

  function show_form(){
     //$form_show();
    if (visibility == ""){
      visibility="true";
    }
    else{
      visibility = "";
    }
  }
  
</script>

<style>
  paper-card {
    width: 33%;
    margin: 3px;
    padding: 10px;
    background-color: lightskyblue;
  }
  mwc-top-app-bar {
  --mdc-theme-primary: rgb(241, 251, 255);
  --mdc-theme-on-primary: rgb(0, 0, 0);
  }
  main{
    background-color: rgb(241, 251, 255);
  }
</style>

<link href="https://fonts.googleapis.com/icon?family=Material+Icons"
      rel="stylesheet">

<main>
    <DB bind:documents={books} initsrc="./books.json" collection="books"/>
    <mwc-top-app-bar centerTitle>
    	<mwc-icon-button icon="menu" slot="navigationIcon"></mwc-icon-button>
    	<div slot="title">Le Fnack</div>
    	<mwc-icon-button on:click={show_form} icon="add" slot="actionItems"></mwc-icon-button>
    	<div><!-- content --></div>
		</mwc-top-app-bar>
    
    <form class="form_add" hidden="{visibility}">
      <input bind:value={data.title} id="titre" type="text" placeholder="Titre">
      <input bind:value={data.image} id="url" type="text" placeholder="URL de l'Image">
      <input bind:value={data.auteur} id="auteur" type="text" placeholder="Auteur">
      <input bind:value={data.prix} id="prix" type="text" placeholder="Prix">
      <input bind:value={data.url} id="url" type="text" placeholder="Url Achat">
      <button type="submit" on:click={ addBook }>Ajouter</button>
    </form>
  
  {#each books as book}
    <paper-card heading={book.title}>
	  <img src="data:image/jpeg;base64, {book.img.data}" alt="{book.title}" title="{book.title}"> 	
      <div class="card-content">
        Ecrit par {book.author} et vendu à seulement {book.price}€.
      </div>
      <div>
        <button on:click={modify}>Modifier</button>
        <button on:click={book => onDelete(book)}>Supprimer</button>
      </div>
    </paper-card>
  {/each}
</main>
