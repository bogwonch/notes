A lay explanation of this whole Dell thing you may have seen in the news:

Your computer has a whole bunch of keys on it (if you’re on a Mac have a look in the Keychain Access app under System Roots, if your in Linux its usually in /etc/ssl/cacertificates.crt if I remember, Windows has it in the registry I think but its been a decade since I used Windows).

These keys are all by companies and governments to prove that they say a website is secure. When you visit a page using https it presents some evidence that it claims prove it is who it says it is. If when we check this evidence it leads back to one of these keys then we trust it, otherwise we do not and you get a browser warning saying the certificate is not valid.

Keys come in pairs: ones which can be used to check the evidence, and others which can make the evidence in the first place. All these keys are (normally) of the first kind.

What Dell (and what Lenovo did earlier in the year) is add two new keys. One of the evidence-checking kind, and one of the evidence-creating kind.

What *probably* they wanted to do was to try and get some information about you so they could make targeted advertisements for you. To do this they need to look at what you’re browsing: so when you ask for an https website they act a bit like a go between between you and the website. You ask them for the data, they fetch and inspect it before handing it back to you, when you send the site data they take it from you and send it to the site on your behalf. To do this they need one key so you can check their certificate, and another which lets them create new certificates so that when you want to talk to Google (or whoever) they can create the evidence that you’re talking to them on Google’s behalf.

If I were to do this to secretly look at your internet data we’d call it a man-in-the-middle attack. Done corporately like this we might call it *targeted advertising*, or *analytics*. Its definitely dodgy and easy to screw up, but it isn't necessarily bad. It’s a fairly common practice.

Of course they have screwed it up.

The problem is how they made the keys. Every computer Dell produced contained the same keys. This means that if you buy a Dell you are given a key that lets you produce evidence that you are any website, and that evidence will be accepted by any other dell computer.

What they should have done is make a new key pair for every computer so they can still get your traffic but that only the key on your computer can decrypt it. That way if your computer is compromised only your computer is screwed up. They way they did it if ANY dell computer is compromised or has those keys extracted then EVERY dell computer is potentially compromised.

When you enter a password or your credit card on a website you (should) look for a little green lock icon that means the website is secure right? So how do you know if the lock is green the evidence is for the real website or a fake produced by this bad Dell key that everyone now has. If you get it wrong someone is going to be able to see your password or bank details.

This is obviously bad.

What can you do about it? Get rid of that key. Unfortunately their software they bundle with their computer will reinstall it for you. You need to get rid of that too. There will probably be a bit of software released by them in the next 24 hours to do just this.

But lets think about the bigger picture for a second: on my computer I have 181 keys that can be used to check evidence that a site is who they say they are. All of them have evidence producing keys that can make proofs you’re visiting any website. Are you *sure* you trust all of them to not produce a fake key to sell you better advertising? Because a government asked them to and made them sign a secrecy agreement so they could catch neer-do-wells? Because they screwed up and couldn’t be bothered to check someone was who they said they were? There have been cases of all these things happening before.

The picture I attached is of one of the keys on my computer. It belongs (I believe) to the Finnish Population Register Center. Why the hell do they need a key? Why is it in the default set of keys installed on every computer? AND WHY CANT THEY BE BOTHERED TO UPDATE THE EVIDENCE FOR THEIR OWN BLEEDING WEBSITE (WHICH LEADS BACK TO THEIR OWN DAMN KEY) WHICH IS NOW APPARENTLY OUT OF DATE?

Manufacturers adding extra keys on their own computers is only the start of the festering dung heap that is the certificate infrastructure.

![Bad SSL certificate](https://raw.githubusercontent.com/bogwonch/notes/master/dell-ssl-screwup.png)
