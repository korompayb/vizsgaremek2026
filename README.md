# Vizsgaremek 2026
**Participants:** Korompay Bertalan, Szabó Erik

**Topic:** IT system operator and manager. Networking specialist.<br>
**Tools:** VS Code, Figma, Trello, Git, Canonical Ubuntu LTS 24.04, Microsoft Windows 11

> [!TIP]
> Please read this segment very carefully!

**The previous:** At this point I really need to improve my language skill as hard as I can. I'm prepairing this huge IT exam and i really want to do it well.
My first sight of this challenge is quite different, I found it exciting rather than others. I's my chance to do my best and create somenthing big and extraordinary. So let's start at the beginning.

## The beginning

**The first arrangement:** Me and Erik had a conversation about the company that we will building up, create a network interface and also working devices. Our goal is to create a **insurance company**.

At first it seems hard to understand how an insurance company operates. But maybe we will start with the basics, create a network for the workers.

> [!IMPORTANT]  
> We all know, an insurance copany's network should be more complex.

<div align="center">
    <img  src="Screenshot 2025-10-29 at 22.11.46.png" width="400" height="300">
</div>

So basically that should be our network at first, but I know its really easy and problematic, theres a lot of problem with this topology.

> [!NOTE] Megjegyzés 
> Szóval hivatalosan Erikkel még nem álapodtam meg a topológiában, azt viszont már eldöntöttük hogy ezt fogjuk csinálni (Biztosító cég)


<!-- > [!WARNING]  
> warning message.

> [!CAUTION]
> caution -->

> So that's all for 29th of October 2025.

<hr>


> 2026 02 19
<section class="topology">
<section class="router" data-name="c1" data-address="10.0.0.2" data-mask="255.255.255.252"></section>
<section class="router" data-name="c2" data-address="10.0.0.6" data-mask="255.255.255.252"></section>
<section class="router" data-name="c3" data-address="10.0.0.10" data-mask="255.255.255.252"></section>
<br>
<table>
<thead>
<tr>
<th>Kapcsolat</th>
<th>H&aacute;l&oacute;zat</th>
<th>Telephely router</th>
<th>Cluster router</th>
</tr>
</thead>
<tbody>
<tr>
<td>Office &harr; Cluster R1</td>
<td>10.0.0.0/30</td>
<td>10.0.0.1</td>
<td>10.0.0.2</td>
</tr>
<tr>
<td>Datacenter &harr; Cluster R2</td>
<td>10.0.0.4/30</td>
<td>10.0.0.5</td>
<td>10.0.0.6</td>
</tr>
<tr>
<td>Customer Service &harr; Cluster R3</td>
<td>10.0.0.8/30</td>
<td>10.0.0.9</td>
<td>10.0.0.10</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
</section>
