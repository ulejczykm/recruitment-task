<template>
    <main id="sliderView">
        <ul>    
            <li v-for="(photo, index) in url" :key="index" class="slide"> 
                    <img :src="`http://source.unsplash.com/${photo}`" class="imgInSlide" alt="">  
            </li>
        </ul>
        <div @click="nextImages(url.length)">button</div>
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
        var newArr = new Array();

        fetch('https://picsum.photos/v2/list')
        .then(res => {
            return res.json()
        })
        .then(data =>  {
            const arrayOfURL = data.map(photo => {
                return photo.url;
            });
           
            for (let i = 0; i < arrayOfURL.length; i++) {
                let el = arrayOfURL[i];
                let slug = el.slice(el.lastIndexOf("/") + 1); 
                arrayOfSlugs.push(slug);
            }  
            for (let i = 0; i < 3; i++) {
                 let newElement = arrayOfSlugs.shift();
                 newArr.push(newElement);
            }
            this.url =  newArr;
            this.restOfArray = arrayOfSlugs;
            console.log(newArr)
            console.log( this.restOfArray)
            
        })
        .catch(error => console.log(error))
    },
    nextImages(){
        const arrayOfSlugs = this.restOfArray;  
        const newArr = [];
 
            for (let i = 0; i < 3; i++) {
                 let newElement = arrayOfSlugs.shift();
                 newArr.push(newElement);
            }
            this.url =  newArr;
            this.restOfArray = arrayOfSlugs;
            console.log(newArr)
            console.log(arrayOfSlugs)

            if (arrayOfSlugs.length <=0){
                fetch('https://picsum.photos/v2/list')
                .then(res => {
                    return res.json()
                })
                .then(data =>  {
                    const arrayOfURL = data.map(photo => {
                        return photo.url;
                    });
                
                    for (let i = 0; i < arrayOfURL.length; i++) {
                        let el = arrayOfURL[i];
                        let slug = el.slice(el.lastIndexOf("/") + 1); 
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
ul {
    list-style-type: none;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: black;
    width: 100%;
    height: 100vh;
}
li {
  display: block;
 
  padding: 0;
  list-style: none;
  &:nth-child(-n+3)
  {
      width: 33%;
  }
}
a {
  color: #42b983;
}
 #sliderView {
    width: 100%;
    height: 100%;
}
/* .slide {
    width: 500px;
    height: 500px;
} */
.imgInSlide{
    width: 100%;
    height: auto;
    object-fit: cover;
}
</style>
