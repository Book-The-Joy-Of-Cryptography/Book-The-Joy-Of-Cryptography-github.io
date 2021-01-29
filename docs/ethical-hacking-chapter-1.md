---
layout: default
title: Chapter 1 
parent: Ethical Hacking
nav_order: 30010
---

<button class="btn js-toggle-dark-mode">Dark mode</button>

<script>
const toggleDarkMode = document.querySelector('.js-toggle-dark-mode');

jtd.addEvent(toggleDarkMode, 'click', function(){
  if (jtd.getTheme() === 'dark') {
    jtd.setTheme('light');
    toggleDarkMode.textContent = 'Preview dark color scheme';
  } else {
    jtd.setTheme('dark');
    toggleDarkMode.textContent = 'Return to the light side';
  }
});
</script>

<div style='font-family: "Times New Roman", Times, serif; font-size: 18px' markdown="1">

1. TOC
{:toc}

### Chapter 1 
{: .no_toc}

## Why Ethical Hacking?
This book aims to explore the issue of ethical hacking from an unconventional and unique viewpoint, one that draws upon my own vast experience in this area. My background spans seventeen years and has incorporated roles as a law and cyber-security professor, human-rights activist, cyber-policy consultant, technology developer, and cybercrime investigation advisor. It is this experience that I will draw upon to form the pillars of the book, which departs from some of the conventional thinking in this area. This is not a book about Anonymous or about hacking organizations per se, though case studies from various incidences are certainly explored. This book is about various types of activities that are often referred to as “ethical hacking”—hacking for an ethical reason—whereby it will be argued that law and policy ought not to be the same here as for those hacking activities that are purely for economic gain or to cause harm or mischief. As will be seen, I have grouped ethical hacking into five groups:
* online civil disobedience;
* hacktivism;
* penetration testing and security-vulnerability disclosure; • counterattack/hackback; and
* security activism.

Let us start this journey first by talking briefly about you, about me, and then a lot about ethical hacking.

### 1.1 **You**

The book is designed to cater to a broad spectrum of readers, ranging from cyber-security experts and policy-makers to academics. Despite its intended primary audience, the book has also been written in such a manner as to make it accessible not only to university students but the broader general public. The complexity and rate of change seen within areas of technology, cyber security, and ethical hacking make it essential not to assume that you are across all terminology. There are many terms that common media and blogs use incorrectly or interchangeable, such as “computer virus,” which turns out to be a “computer worm.” Other new methods of malicious-software propagation may emerge that a reader would not necessarily be familiar with. In general, ethical hacking involves many technical terms that require a foundational level of understanding in order to better understand policy and other issues. For example, a denial-of-service attack is potentially lawful if your own device is used to participate in an online political protest. It would not be lawful to use a botnet that connects to unknown or third-party devices to participate in the same protest. The aim is to provide you with digestible material that demonstrates concepts through engaging case studies. These case studies of ethical hacking, spanning the last twenty years, are dissected and catalogued in a manner that identifies the groups and movements, their motivations, and the techniques they used. You will see some of the most notorious of these incidences explored referenced in chapters 4–6, then selected incidences are looked in context and by issues in chapters 7–13.  

If you are a policy-maker, chapters 3–7 and 14 are essential reading. Chapter 3 provides the only publicly available quantitative analysis of ethical hacking in the world. The stark numbers contained within this chapter will assist you in demonstrating why the decisions and policies you recommend are fundamentally essential. As a policy-maker, you are all too aware that in a world of cleverly masked sensationalism posing as substantive information it has become difficult to discern what information can be trusted. Chapters 4–6 table legal cases and selected noteworthy incidences from the quantitative analysis. Throughout chapters 7–13 I aim to provide you with intricate and, at times, intimate looks at the world of ethical hacking, which will assist you in generating well-informed and robust policy. Chapter 14 discusses the required frameworks and changes required as a matter of both policy and law.  


If you are a cyber-security expert or consider yourself a hacktivist, there are ethical and legal issues contained within this book that are essential reading. This includes policy and legal lines to be cautious of, which could easily see you cross from that of “ignore action with caution” to one of “prosecute” by authorities. These cautionary tales are drawn from my experience undertaking a large range of roles, as described above.  

As I know all too well, the issues surrounding cyber security have garnered interest from a broad demographic of society, and is not limited to just policy-makers, experts, and academics. Even if you do not fit within any of the three later categories, I would still love for you to drop me a line at alanacybersecurity.com and let me know your background. While I keep analytics on how many people visit the site, and the general geographic area of the IP addresses, this will give me an opportunity to engage with you and understand the broader community interests. But please remember that if you are looking at the site or wish to contact me about a private or sensitive matter, this site offers no anonymity to you. So, connect with a VPN, proxy or other anonymizer such as TOR.  
www.alanacybersecurity.com  

There is also the option of communicating later using encryption and, for journalists, I have and use Signal.
</div>

<div style='text-align:right' markdown="1">
[Next](/docs/1984-part-3-chapter-2/)
</div>
