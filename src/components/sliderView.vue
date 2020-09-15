<template>
    <main id="sliderView">
        <ul>    
            <li v-for="(photo, index) in url" :key="index" class="slide"> 
                    <img :src="`http://source.unsplash.com/${photo}`" class="imgInSlide" alt="">  
            </li>
        </ul>
        <button id="send" @click="nextImages()">Next</button>
    </main>
</template>

<script>
export default {
  name: 'SliderView',

  data: () => ({
        url: [],
        restOfArray: [] 
  }),
  mounted() {
      this.startFetch();
  },
  methods: {
    startFetch(){
          
        const arrayOfSlugs = new Array();  
        const displayedSlugs = new Array();

        fetch('https://picsum.photos/v2/list')
        .then(res => {
            return res.json()
        })
        .then(data =>  {
            const arrayOfURLs = data.map(photo => {
                return photo.url;
            });
           
            for (let i = 0; i < arrayOfURLs.length; i++) {
                let fullURL = arrayOfURLs[i];
                let slug = fullURL.slice(fullURL.lastIndexOf("/") + 1); 
                arrayOfSlugs.push(slug);
            }  
            for (let i = 0; i < 3; i++) {
                 let newSlug = arrayOfSlugs.shift();
                 displayedSlugs.push(newSlug);
            }
            this.url = displayedSlugs;
            this.restOfArray = arrayOfSlugs;
    /*         console.log(displayedSlugs)
            console.log( this.restOfArray) */
            
        })
        .catch(error => console.log(error))
    },
    nextImages(){
        const arrayOfSlugs = this.restOfArray;  
        const displayedSlugs = [];
 
        for (let i = 0; i < 3; i++) {
                let newElement = arrayOfSlugs.shift();
                displayedSlugs.push(newElement);
        }
        this.url = displayedSlugs;
        this.restOfArray = arrayOfSlugs;
     /*    console.log(displayedSlugs)
        console.log(arrayOfSlugs) */

        if (arrayOfSlugs.length <=0){
            fetch('https://picsum.photos/v2/list')
            .then(res => {
                return res.json()
            })
            .then(data =>  {
                const arrayOfURLs = data.map(photo => {
                    return photo.url;
                });
            
                for (let i = 0; i < arrayOfURLs.length; i++) {
                    let fullURL = arrayOfURLs[i];
                    let slug = fullURL.slice(fullURL.lastIndexOf("/") + 1); 
                    arrayOfSlugs.push(slug);
                } 
            })
            .catch(error => console.log(error))
            this.restOfArray = arrayOfSlugs;
        }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

h3 {
  margin: 40px 0 0;
}
main {
    width: 100%;
    height: 100vh;
    background-color: black;
}
ul {
    list-style-type: none;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 90%;
}
li {
  display: block;
 
  padding: 0;
  list-style: none;
  &:nth-child(-n+3)
  {
      width: 33%;
      height: 70%;
  }
}
a {
  color: #42b983;
}
.imgInSlide{
    width: 100%;
    height: 100%;
    object-fit: cover;
}
 #send
    {
        border: 0px;
        width: 20%;
        height: 50px;
        outline: var(--background-beige);
        color: black;
        font-size: 1.5vw;
        font-weight: bold;
        /* background-size:1px 170px;  */
       /*  background-image: radial-gradient(circle, rgb(199, 154, 153) 0%, rgb(208, 174, 164) 100%); */
       background-image: url(~@/assets/wood.jpg);
        display: inline-block;
        cursor: pointer;
        text-decoration: none;
        text-shadow: black;
        transition: all 0.1s;
        &:active 
        {
            position: relative;
            background-position: 0px 0.5px; 
            top: 1px;
        }
    }
</style>
