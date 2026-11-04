---
title: "React & Tailwind CSS Tooltip Component"
date: 2023-01-21T10:43:00Z
categories:
  - "notebook"
tags:
  - "tailwindcss"
  - "react"
  - "font-awesome"
source: Notion
---

## Tooltip component

```javascript
export function Tooltip({ content }) {
  return (
		{/* Tailwind's group class will be used trigger the hover */}
    <span className="group">

			{/* Font Awesome question mark icon */}
      <i className="fas fa-question-circle text-black group-hover:scale-120 ml-1 cursor-pointer ease-in-out duration-300 transition-transform"></i>
      <span
        className={`
              w-full absolute rounded shadow 
              text-sm font-normal font-sans bg-black text-white p-2 
              pointer-events-none group-hover:pointer-events-auto
              opacity-0 group-hover:opacity-100
              left-0 -top-3 -translate-y-3/4 group-hover:-translate-y-full 
              ease-in-out duration-300 transition-[transform,opacity]`}
      >

				{/* Set inner HTML or just output content in an expression */}
        <span dangerouslySetInnerHTML={{ __html: content }}></span>

        {/* Optional speech balloon triangle pointing down */}
        <div
          className={`
              absolute w-0 h-0 border-l-4 border-t-4 border-r-4 border-b-4 border-black 
              left-1/2 -translate-x-2 -bottom-1 transform rotate-45 shadow`}
        ></div>

      </span>
    </span>
  );
}
```

## How to use Tooltip

```javascript
import { Tooltip } from "./Tooltip";

export function ParentComponent() {

	// This can be purely text or html
	const content = "This is a tooltip message.";

	return (
		{/* Tooltip will be positioned relative to the element with the 'relative' class */}
    <div className="relative">
      {content && (
        <Tooltip content={content} />
      )}
    </div>
	);
}
```
