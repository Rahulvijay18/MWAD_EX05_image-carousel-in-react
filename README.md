# MWAD_EX05_image-carousel-in-react

## Date: 08/10/2025

## AIM
To create a Image Carousel using React 

## ALGORITHM
### STEP 1 Initial Setup:
Input: A list of images to display in the carousel.

Output: A component displaying the images with navigation controls (e.g., next/previous buttons).

### Step 2 State Management:
Use a state variable (currentIndex) to track the index of the current image displayed.

The carousel starts with the first image, so initialize currentIndex to 0.

### Step 3 Navigation Controls:
Next Image: When the "Next" button is clicked, increment currentIndex.

If currentIndex is at the end of the image list (last image), loop back to the first image using modulo:
currentIndex = (currentIndex + 1) % images.length;

Previous Image: When the "Previous" button is clicked, decrement currentIndex.

If currentIndex is at the beginning (first image), loop back to the last image:
currentIndex = (currentIndex - 1 + images.length) % images.length;

### Step 4 Displaying the Image:
The currentIndex determines which image is displayed.

Using the currentIndex, display the corresponding image from the images list.

### Step 5 Auto-Rotation:
Set an interval to automatically change the image after a set amount of time (e.g., 3 seconds).

Use setInterval to call the nextImage() function at regular intervals.

Clean up the interval when the component unmounts using clearInterval to prevent memory leaks.

## PROGRAM

### ImageCarousel.jsx
```
import { useState, useEffect, useCallback } from "react";
const ImageCarousel = ({ images, autoRotateInterval = 3000 }) => {
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isAutoRotating, setIsAutoRotating] = useState(true);
  const [direction, setDirection] = useState("next");

  const nextImage = useCallback(() => {
    setDirection("next");
    setCurrentIndex((prevIndex) => (prevIndex + 1) % images.length);
  }, [images.length]);

  const prevImage = useCallback(() => {
    setDirection("prev");
    setCurrentIndex((prevIndex) => (prevIndex - 1 + images.length) % images.length);
  }, [images.length]);

  const goToImage = (index) => {
    setDirection(index > currentIndex ? "next" : "prev");
    setCurrentIndex(index);
  };

  useEffect(() => {
    if (!isAutoRotating) return;

    const interval = setInterval(() => {
      nextImage();
    }, autoRotateInterval);

    return () => clearInterval(interval);
  }, [isAutoRotating, nextImage, autoRotateInterval]);

  const handleMouseEnter = () => setIsAutoRotating(false);
  const handleMouseLeave = () => setIsAutoRotating(true);

  return (
    <div className="carousel-container" onMouseEnter={handleMouseEnter} onMouseLeave={handleMouseLeave}>
      <div className="carousel-images">
        {images.map((image, index) => (
          <div
            key={index}
            className={`carousel-slide ${
              index === currentIndex ? "active" : ""
            }`}
          >
            <img src={image.src} alt={image.alt} className="carousel-img" />
            {image.title && <div className="carousel-title">{image.title}</div>}
          </div>
        ))}

        <button className="carousel-btn prev" onClick={prevImage}>
          &#10094;
        </button>
        <button className="carousel-btn next" onClick={nextImage}>
          &#10095;
        </button>
        <button className="carousel-btn play-pause" onClick={() => setIsAutoRotating(!isAutoRotating)}>
          {isAutoRotating ? "❚❚" : "▶"}
        </button>
      </div>

      <div className="carousel-dots">
        {images.map((_, index) => (
          <span
            key={index}
            className={`dot ${index === currentIndex ? "active-dot" : ""}`}
            onClick={() => goToImage(index)}
          ></span>
        ))}
      </div>
    </div>
  );
};

export default ImageCarousel;

```

### Carousel.css
```
.carousel-container {
  position: relative;
  max-width: 900px;
  margin: 50px auto;
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
  background: #fff;
}

.carousel-images {
  position: relative;
  width: 100%;
  height: 450px;
}

.carousel-slide {
  position: absolute;
  width: 100%;
  height: 100%;
  opacity: 0;
  transform: scale(0.95);
  transition: all 0.5s ease-in-out;
}

.carousel-slide.active {
  opacity: 1;
  transform: scale(1);
  z-index: 10;
}

.carousel-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 12px;
}

.carousel-title {
  position: absolute;
  bottom: 20px;
  left: 20px;
  color: #fff;
  font-size: 1.8rem;
  font-weight: bold;
  text-shadow: 2px 2px 6px rgba(0,0,0,0.5);
}

.carousel-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(255,255,255,0.8);
  border: none;
  padding: 10px;
  cursor: pointer;
  font-size: 1.5rem;
  border-radius: 50%;
  transition: background 0.3s;
}

.carousel-btn:hover {
  background: rgba(255,255,255,1);
}

.carousel-btn.prev {
  left: 15px;
}

.carousel-btn.next {
  right: 15px;
}

.carousel-btn.play-pause {
  top: 15px;
  right: 15px;
  transform: none;
  font-size: 1.2rem;
}

.carousel-dots {
  text-align: center;
  padding: 15px 0;
  background: #f1f1f1;
}

.dot {
  display: inline-block;
  height: 12px;
  width: 12px;
  margin: 0 5px;
  background-color: #bbb;
  border-radius: 50%;
  cursor: pointer;
  transition: background 0.3s;
}

.active-dot {
  background-color: #717171;
}

```

### App.jsx
```
import { BrowserRouter, Routes, Route } from "react-router-dom";
import ImageCarousel from "./ImageCarousel";
import Index from "./pages/Index";
import NotFound from "./pages/NotFound";

const App = () => {
  const images = [
    { src: "https://picsum.photos/id/1018/900/450", alt: "Image 1", title: "Beautiful Landscape" },
    { src: "https://picsum.photos/id/1025/900/450", alt: "Image 2", title: "Serene Beach" },
    { src: "https://picsum.photos/id/1035/900/450", alt: "Image 3", title: "Majestic Mountains" },
    { src: "https://picsum.photos/id/1043/900/450", alt: "Image 4", title: "Starry Night" }
  ];

  return (
    <BrowserRouter>
      <Routes>
        <Route
          path="/"
          element={<ImageCarousel images={images} />}
        />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
};

export default App;

```

## **OUTPUT**


<img width="1901" height="903" alt="Screenshot 2025-10-08 212623" src="https://github.com/user-attachments/assets/2059ed91-7cbc-42f0-ac0f-458edc2cbbe5" />

---
<img width="1901" height="903" alt="Screenshot 2025-10-08 212635" src="https://github.com/user-attachments/assets/defbdaef-8510-44df-afb3-71bd58c60ec6" />

---
<img width="1899" height="897" alt="Screenshot 2025-10-08 212646" src="https://github.com/user-attachments/assets/310a4aa0-8d94-44b0-8edf-f8d3f2203da4" />

---
<img width="1899" height="897" alt="Screenshot 2025-10-08 212737" src="https://github.com/user-attachments/assets/db5caa02-46e9-44f4-9705-afcaacbb4617" />


---
<img width="1893" height="893" alt="Screenshot 2025-10-08 212746" src="https://github.com/user-attachments/assets/bac79c34-7ed9-4d0d-8275-20dc70a2eb4e" />


---


## RESULT
The program for creating Image Carousel using React is executed successfully.
