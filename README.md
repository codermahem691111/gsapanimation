import './App.css'

import React, { useState } from 'react'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import LocomotiveScroll from 'locomotive-scroll';
import { useEffect,useRef } from 'react';
gsap.registerPlugin(ScrollTrigger);

function App() {
  const containerRef = useRef(null);

  useEffect(() => {
    const scroll = new LocomotiveScroll({
      el: containerRef.current,
      smooth: true,
    });

    scroll.on('scroll', ScrollTrigger.update);

    ScrollTrigger.scrollerProxy(containerRef.current, {
      scrollTop(value) {
        return arguments.length
          ? scroll.scrollTo(value, 0, 0)
          : scroll.scroll.instance.scroll.y;
      },
      getBoundingClientRect() {
        return { top: 0, left: 0, width: window.innerWidth, height: window.innerHeight };
      },
      pinType: containerRef.current.style.transform ? 'transform' : 'fixed',
    });

    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: '.accordions',
        scroller: containerRef.current,
        pin: true,
        start: 'top top',
        end: 'bottom top',
        scrub: 1,
        ease: 'linear',
      },
    });

    tl.to('.accordion .text', {
      height: 0,
      paddingBottom: 0,
      opacity: 0,
      stagger: 0.5,
    });
    tl.to('.accordion', {
      marginBottom: -15,
      stagger: 0.5,
    }, '<');

    ScrollTrigger.addEventListener('refresh', () => scroll.update());
    ScrollTrigger.refresh();

    // Cleanup on component unmount
    return () => {
      scroll.destroy();
      ScrollTrigger.getAll().forEach(trigger => trigger.kill());
    };
  }, []);
  return (
    <div>
       <div ref={containerRef} className="overflow-hidden">
      <div className="px-4 space-y-10">
        <div className="accordions">
          <div className="accordion bg-blue-500 text-white p-5 rounded-lg">
            <div className="text">Accordion 1</div>
          </div>
          <div className="accordion bg-green-500 text-white p-5 rounded-lg">
            <div className="text">Accordion 2</div>
          </div>
          <div className="accordion bg-purple-500 text-white p-5 rounded-lg">
            <div className="text">Accordion 3</div>
          </div>
        </div>
      </div>
    </div>   
     </div>
  )
}

export default App
