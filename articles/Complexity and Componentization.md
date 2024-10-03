# Complexity and Componentization

Code complexity grows quietly if we’re not careful. It sneaks in through hidden abstractions, bad practices, and leftover junk. Let’s break down some common issues.


## Unknown Unknowns

<div style="display: flex; align-items: center; margin-bottom: 30px;">
  <img src="https://bank.cellar-c2.services.clever-cloud.com/file/project/2bcc599a6d3448cac599dbf86985b947/5w7z54.jpg" alt="Unknown Unknowns" style="width: 300px; margin-right: 30px;">
  <div>
    <p>These are the hidden problems you didn’t know existed until they cause headaches. What this looks like:</p>
    <ul>
      <li><strong>Magic formulas</strong> – Maths or logic with no context. No one knows why it's there, but no one dares to touch it.</li>
      <li><strong>Mixed logic</strong> – Business logic jammed into services. Now it’s a mess no one wants to clean up.</li>
      <li><strong>Dead code</strong> – Unused code that everyone’s too afraid to delete.</li>
      <li><strong>Leftover props</strong> – Components dragging around props that may or may not be needed. No one knows, so they just stay.</li>
    </ul>
  </div>
</div>

## Change Amplification
<div style="display: flex; align-items: center; margin-bottom: 30px;">
  <img src="https://bank.cellar-c2.services.clever-cloud.com/file/project/2512276ef192d2fad1188985b14b49f6/DALL%C2%B7E%202024-10-02%2015.58.28%20-%20A%20humorous%20but%20meaningful%20meme%20illustrating%20%27Change%20Amplification%27%20in%20software%20development.%20Show%20a%20domino%20effect%20where%20the%20first%20small%20domino%20is%20label.webp" alt="Change Amplification" style="width: 300px; margin-right: 30px;">
  <div>
    <p>A small bad decision spreads quickly. Once someone sees it, they copy it everywhere, and now it’s a pattern for no good reason. What this looks like:</p>
    <ul>
      <li><strong>"Just this once" mentality</strong> – You make an exception in the code thinking it won't hurt. Suddenly it’s everywhere, and no one remembers why it was added in the first place.</li>
      <li><strong>Pointless props</strong> – Adding a prop to avoid duplicating a few lines of code, but now the next person has to deal with a more complicated component for no reason.</li>
    </ul>
  </div>
</div>


## Cognitive Load

<div style="display: flex; align-items: center;">
  <img src="https://bank.cellar-c2.services.clever-cloud.com/file/project/76cdac59a9389cef53e81be3c4eedb4c/DALL%C2%B7E%202024-10-02%2015.51.41%20-%20A%20meme%20illustrating%20cognitive%20load%20in%20software%20development.%20Show%20a%20developer%20sitting%20at%20their%20desk%2C%20with%20their%20brain%20exploding%20from%20too%20many%20thought%20b.webp" alt="Cognitive Load" style="width: 300px; margin-right: 30px;">
  <div>
    <p>The more abstractions you add, the more mental effort it takes to understand what's happening. Shallow functions can make things worse by spreading logic across too many disconnected pieces. A deeper function with clear flow reduces this burden. What this looks like:</p>
    <ul>
      <li><strong>Unnecessary abstraction</strong> – Chopping up logic into too many small functions adds layers of confusion. A clear, deep function with purpose is easier to follow.</li>
      <li><strong>Avoid nested if...else</strong> – It is a nightmare to understand what is going on and when! Instead, prefer functions that call other functions for better readability and structure.</li>
      <li><strong>Readable but not at first glance</strong> – It’s okay if it takes a few more seconds to read as long as it’s well-structured. Avoid tricks like ternary operators that make someone pause and rethink the flow.</li>
    </ul>
  </div>
</div>
>


## When to Duplicate / Not Duplicate

Duplicate when it keeps things simple and avoids over-abstraction. A few lines of duplicate code are often better than a complex reusable component.

The three schools of duplication:

1. **DRY** (“Don’t repeat yourself”) – Outdated good practice. Fixing the same bug in 7 places led to this concept. The solution would be to componentize right away.
2. **WET** (“Write Everything Twice”) – Recognize patterns and componentize after the third time.
3. **AHA** – “Avoid Hasty Abstractions.” Always prefer duplication over unnecessary abstraction. Duplicating is always cheaper than getting the abstraction wrong.

## The Abstraction Pitfall

Programmer A sees duplication and extracts it into a new abstraction, replacing the duplicated code. Time passes and new requirements arise that fit the abstraction…almost. Programmer B alters it with parameters and conditionals, and suddenly the abstraction behaves differently. Over time, the code becomes a condition-laden procedure. You appear in the story, and the code is now incomprehensible.

When you encounter this, resist the sunk cost fallacy. Instead:

- Reintroduce duplication by inlining the abstracted code back into each caller.
- Remove unnecessary bits for each caller, keeping only what’s needed.

This removes the wrong abstraction and reduces the cognitive load. It may feel counterintuitive, but the fastest way forward is back. Once you inline the code, the right abstraction often becomes clearer.

## Optimise for Change First

We don’t know the future of code, and optimizing too early can result in wasted effort. Code changes; if it doesn’t, who cares how it looks? When you abstract too early, you end up bending your code to fit new use cases until the abstraction becomes a mess of conditionals and loops.

The takeaway from AHA Programming is not to be dogmatic. Write the abstraction when it feels right, and don’t be afraid to duplicate code until you reach that point.

## Conclusion

A pragmatic approach is key. That’s why I prefer AHA over DRY or WET. It helps you stay mindful of abstractions without rigid rules on when to extract code. If you find yourself mired in bad abstractions, take heart! Sandi Metz gives great steps for getting out of that mess. Don’t fall into the sunk cost trap – sometimes the fastest way forward is to go back and rethink your abstractions.

Optimize for change and always keep in mind: big components at early stages, componentize as your product matures. In my opinion, prefer WET or AHA for a more flexible and readable codebase.
