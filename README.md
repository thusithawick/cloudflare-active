# List of Sites on Cloudflare DNS (archived)

This is an (archived) list of sites on Cloudflare DNS at the time of the [CloudBleed HTTPS traffic leak](https://blog.cloudflare.com/incident-report-on-memory-leak-caused-by-cloudflare-parser-bug/) announcement.
Original vuln [thread](https://bugs.chromium.org/p/project-zero/issues/detail?id=1139) by Google Project Zero.

Cloudflare has posted a very detailed response, explaining exactly what the implications of this leak are.  It thoroughly explains their language in earlier statements, and I highly recommend reading it before looking through this list for domains:
https://blog.cloudflare.com/quantifying-the-impact-of-cloudbleed/

### DISCLAIMER:
This list is archived and no longer under active maintenance.  It may contain stale or inaccurate data that will not be corrected.  Do not link to it from press releases, it is not intended for end-users.  If people want to find it, they can Google it.

This list contains *all* domains that use Cloudflare DNS, not just the Cloudflare proxy (the affected service that leaked data).  It's a broad sweeping list that includes everything.  Just because a domain is on the list does not mean the site is compromised, and sites may be compromised that do not appear on this list.

Cloudflare has not provided an official list of affected domains, and likely will not due to privacy concerns.  I've compiled an unofficial list here so you know where to start searching for sessions to reset and passwords to change.

See [issue #127](https://github.com/pirate/sites-using-cloudflare/issues/127#issuecomment-282385955) and [issue #87](https://github.com/pirate/sites-using-cloudflare/issues/87#issuecomment-282372235) for additional info about which sites are likely to be affected.

## Impact

**Between 2016-09-22 - 2017-02-18 session tokens, passwords, private messages, API keys, and other sensitive data were leaked by Cloudflare to random requesters.**
Data was cached by search engines, and may have been collected by random adversaries over the past few months.

Requests to sites with the HTML rewrite features enabled triggered a pointer math bug. Once the bug was triggered the response would include data from ANY other Cloudflare proxy customer that happened to be in memory at the time. Meaning a request for a page with one of those features could include data from Uber or one of the many other customers that didn't use those features. So the potential impact is every single one of the sites using Cloudflare's proxy services (including HTTP & HTTPS proxy).

 "The greatest period of impact was from February 13 and February 18 with around 1 in every 3,300,000 HTTP requests through Cloudflare potentially resulting in memory leakage (that’s about 0.00003% of requests), potential of 100k-200k paged with private data leaked every day" -- 

~~You can see some of the leaked data yourself in search engine caches: https://duckduckgo.com/?q=+%7B%22scheme%22%3A%22http%22%7D+CF-Host-Origin-IP&t=h_&ia=web~~ (2/25/2017) DuckDuckGo has removed this data

Confirmed affected domains found in the wild: http://doma.io/2017/02/24/list-of-affected-cloudbleed-domains.html

## What should I do?

The most important thing you can do is ask your vendors and sites to reset all their session tokens, as more response data was leaked than request data, and responses generally contain session tokens rather than passwords.
If websites you use have a button to "log out all active sessions", use it.  Since sites may be compromised this week due to data discovered in caches, it's best to also do this again in a week or two after everything settles down.
If websites you use don't have an option to log out all active sessions, contact them and pressure them to rotate all their session tokens.

To be extra safe, you may want to check your password managers and change crucial passwords, especially those on these affected sites.
Rotate API keys & secrets, and confirm you have 2-FA set up for important accounts.  This might sound like fear-mongering, but the scope of this leak is truly massive, and due to the fact that *all* Cloudflare proxy customers were vulnerable to having data leaked, many of the extra cautious people out there would rather be safe than sorry.

Theoretically sites not in this list can also be affected (because an affected site could have made an API request to a non-affected one).

## Methodology

This list was compiled from 3 large dumps of all Cloudflare customers provided by crimeflare.com/cfs.html, and several manually copy-pasted lists from stackshare.io and wappalyzer.com.
Crimeflare collected their lists by doing NS DNS lookups on a large number of domains, and checking SSL certificate ownership.

I scraped the Alexa top 10,000 by using a simple loop over the list:

```fish
for domain in (cat ~/Desktop/alexa_10000.csv)
    if dig $domain NS | grep cloudflare
        echo $domain >> affected.txt
    end
end
```
The Alexa scrape, and the Crimeflare dumps were then combined in a single text file, and passed through `sort | uniq`.  I've since accepted several PRs and issues to remove sites that were unaffected from the list.

Data sources:
 - https://stackshare.io/cloudflare
 - https://wappalyzer.com/applications/cloudflare
 - DNS scraper I'm running on Alexa top 10,000 sites (grepping for cloudflare in results)
 - https://www.cloudflare.com/ips/  (going to find sites that resolve to these IPs next)
 - http://www.crimeflare.com/cfs.html (scrape of all Cloudflare customers)
 - http://www.doesitusecloudflare.com/

I'd rather be safe than sorry so I've included any domain here that remotely touches Cloudflare.  **Don't point end-users to this list please, it has too many false positives to be useful for non-analytical purposes**.
I'm no longer accepting PRs to remove sites from the list, our previous [process to remove sites](https://github.com/pirate/sites-using-cloudflare/issues/213) was error-prone and labor intensive.  The list is now in archive mode, consider it defunct.
If you think for some reason this will greatly impact you or your users, DM me on twitter.

## Full List

**Download the [full list.zip](https://github.com/pirate/sites-using-cloudflare/archive/master.zip) (22mb)**

4,287,625 possibly affected domains.  Download this file, unzip it, then run `grep -x domaintocheck.com sorted_unique_cf.txt` to see if a domain is present.

Also, a list of some [iOS apps](https://www.nowsecure.com/blog/2017/02/23/cloudflare-cloudbleed-bugs-impact-mobile-apps) that *may* have been affected.

## Search Tools

There are several tools out there to search the list, I wont endorse any here due to them having greatly varying degrees of accuracy.
Please do not make user-facing tools to search the list or cross-reference it with browser history, this list has too many false positives to use for that purpose.
You will make users lose trust in many sites, despite there being less than a 1 in a million chance of them having data leaked.

## Notable Sites

- [bikerrs.com](https://bikerrs.com)
- [chamathkaara.com](https://chamathkaara.com)
- [admonster.lk](https://admonster.lk)
- [jetmash.com](https://jetmash.com)
- [laebuy.lk](https://laebuy.lk)
- [laebuy.com](https://laebuy.com)
