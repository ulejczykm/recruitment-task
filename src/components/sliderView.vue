<template>
    <main id="sliderView">
        <ul>    
            <li v-for="(photo, index) in url" :key="index"> 
            <div class="imageWrapper">
                <img :src="`http://source.unsplash.com/${photo.slug}`" class="imgInSlide" alt=""> 
                <div class="imageOverlay">
                    <button @click="downloadFoto(photo.download, photo.slug)">Download</button>
                </div>
            </div>
            </li>
        </ul>
        <div id="buttonWrapper">
            <button id="next" @click="nextImages()">Next</button>
        </div>
    </main>
</template>

<script>
import Axios from 'axios';

export default {
  name: 'SliderView',

  data: () => ({
        url: [],
        /* restOfArray: [],
        downloadUrl: [],
        wholeArray: [] */
  }),
  mounted() {
      this.startFetch();
  },
  methods: {
    startFetch(){
          
        const displayedSlugs = new Array();
        
        fetch('https://picsum.photos/v2/list')
        .then(res => {
            return res.json()
        })
        .then(data =>  {
            const arrayOfURLs = data.map(photo =>
                ({
                    id: photo.id,
                    slug: (photo.url).slice(photo.url.lastIndexOf('/') + 1),
                    download: photo.download_url
                })
            );        
            arrayOfURLs.forEach((item, i) => {
                item.id = i++;
            }) 

            const wholeArray = [...arrayOfURLs];
            this.wholeArray = wholeArray;

            for (let i = 0; i < 3; i++) {
                let newElement = arrayOfURLs.shift();
                displayedSlugs.push(newElement);
            }

            this.url = displayedSlugs;
            this.restOfArray = arrayOfURLs;
           
        })
        .catch(error => console.log(error))
        
    },
    nextImages(){

        const arrayOfURLs = this.restOfArray;  
        const displayedSlugs = [];
        const wholeArray = this.wholeArray;
        

        for (let i = 0; i < 3; i++) {
            let newElement = arrayOfURLs.shift();
            displayedSlugs.push(newElement);
        }

        this.restOfArray = arrayOfURLs;
        this.url = displayedSlugs;

        if (arrayOfURLs.length <=0){
            this.restOfArray = [...wholeArray] ;
        }
    },
    download(response, slug){
        const url = window.URL.createObjectURL(new Blob([response.data]))
        const link = document.createElement('a')
        link.href = url
        link.setAttribute('download', `${slug}.jpg`)
        document.body.appendChild(link)
        link.click()
    },
    downloadFoto (downloadURL, slug){
      Axios({
        method: 'get',
        url: downloadURL,
        responseType: 'arraybuffer'
      })
      .then(response => {
        this.download(response, slug)  
      })
      .catch(() => console.log('error'))
    }
  }
}
</script>

<style scoped lang="scss">

h3 {
  margin: 40px 0 0;
}
main {
    width: 100%;
    height: 100%;
    background-image: linear-gradient(to top, #fff1eb 0%, #ace0f9 100%);
}
ul {
    list-style-type: none;
    padding: 0;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    flex-flow: row nowrap;
    width: 100%;
    height: 90%;
}
li {
    display: block;
    list-style: none;
    position: relative;
    transition: 0.35s;
    &::before {
        position: absolute;
        top: 0;
        left: 0;
        z-index: -1;
        width: 100%;
        height: 100%;
        background: transparent;
        content: '';
        transition: opacity 0.35s;
        opacity: 0;
        box-shadow: 0 3px 20px rgba(0,0,0,0.8);
    }
    &:hover  {
        transform: scale(1.1);
    }   
    &:hover:before {
        opacity: 1;
    }
    &:hover .imageOverlay {
        left: 0;
        button {
            transform: scale(0.9);
        }
    }
    &:nth-child(-n+3)
    {
        width: 30%;
        height: 65%;
        margin: 0.2%;
    }
}
.imageWrapper {
    position: relative;
    overflow: hidden;
    width: 100%;
    height: 100%;
}
.imgInSlide{
    width: 100%;
    height: 100%;
    object-fit: cover;
}
.imageOverlay {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(to right, rgba(102,84,241,0.2) 0%, rgba(105,234,203,0.7) 100%);
    height: 100%;
    left: 100%;
    top: 0;
    width: 100%;
    transition: all 0.35s;
}
#buttonWrapper{
    height: 10%;
    width: 100%;
}
button
{
    border: 0px;
    width: 200px;
    height: 50px;
    color:white;
    font-size: 26px;
    font-weight: bold;
    border-radius: 50px;
    background-size: 1px 170px; 
    background-image: linear-gradient(0deg, rgba(102,84,241,1) 0%, rgba(105,234,203,1) 100%);
    display: inline-block;
    cursor: pointer;
    text-decoration: none;
    transition: all 0.1s;
    outline: none;
    &:active 
    {
        position: relative;
        background-position: 0px -15px; 
        top: 1px;
        -webkit-tap-highlight-color: transparent;
    }
}
@media (min-width: 0px) and (max-width: 1024px){
    ul {
        flex-flow: column nowrap;
        height: 85%;
    }
    li:nth-child(-n+3)
    {
        width: 90%;
        height: 30%;
        margin: 2%;
    }
    #buttonWrapper{
        button{
          margin-top: 2%;  
        }
    }
}
</style>
