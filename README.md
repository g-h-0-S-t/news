# News
News on your Terminal (pre-requisites: zsh, lolcat, curl, xmlstarlet).  

**Step 0:** Install zsh, lolcat, curl, xmlstarlet ( google them out :P ).  
**Step 1:** Open .zshrc file with the following command in terminal.  
```
open ~/.zshrc
```
**Step 2:** Add the following at the end of your .zshrc file.  
```
news() {
	cmdArg="$1" 
	
	newsSourcess=(
		"Google News|https://news.google.com/rss?hl=en-IN&gl=IN&ceid=IN:en"
		"Bing|https://www.bing.com/news/search?q=latestnews&format=rss"
		"BBC News|http://feeds.bbci.co.uk/news/rss.xml"
		"CNN.com|http://rss.cnn.com/rss/edition.rss"
		"RT World News|https://www.rt.com/rss/news/"
		"Times of India|https://timesofindia.indiatimes.com/rssfeedmostrecent.cms"
		"The New York Times|https://rss.nytimes.com/services/xml/rss/nyt/World.xml"
		"The Guardian|https://www.theguardian.com/world/rss"
		"The Wall Street Journal|https://feeds.a.dj.com/rss/RSSWorldNews.xml"
		"Techmeme|https://www.techmeme.com/feed.xml"		
		"NASA (Breaking News)|https://www.nasa.gov/rss/dyn/breaking_news.rss"
		"NASA (Image of the Day)|https://www.nasa.gov/rss/dyn/lg_image_of_the_day.rss"
		"Manga - Google News|https://news.google.com/rss/search?q=Manga&hl=en-IN&gl=IN&ceid=IN:en"
		"Manga - Bing|https://www.bing.com/news/search?q=Manga&format=rss"
		"Manga - Al's Manga|https://alsmangablog.wordpress.com/feed/"
		"Manga - The Anime Daily|https://www.theanimedaily.com/category/manga/feed/"
		"Manga - Anime Manga Talks|https://animemangatalks.com/feed/"
		"Manga - Anime Outline|https://www.animeoutline.com/feed/"
		"Manga - Anime Hunch|https://animehunch.com/tag/manga-news/feed/"
		"Manga - Astronerd Boy|https://anime.astronerdboy.com/feed"
		"Manga - Coinlocker Baby|http://www.coinlockerbaby.org/feed/"
		"Manga - Honey's Anime|https://honeysanime.com/themes/manga/manga-category/manga-review/feed/"
		"Manga - Immortallium|https://immortalliumblog.com/category/manga/feed/"
		"Manga - KS|https://theksblogs.wordpress.com/category/manga/feed/"
		"Manga - Kodansha|https://kodansha.us/feed/"
		"Manga - Manga Bookshelf|http://mangabookshelf.com/feed/"
		"Manga - Mangafox|https://fanfox.net/rss/devils_line.xml"
		"Manga - Manga Planet|https://mangaplanet.com/feed/"
		"Manga - nntheblog|https://nntheblog.com/category/mangas-blog/feed/"
		"Manga - Ogiue Maniax|https://ogiuemaniax.com/category/manga/feed/"
		"Manga - Otakukart|https://feeds.feedburner.com/otakukartfeed"
		"Manga - Shoujo Henshin|https://shoujohenshin.com/feed/"
		"Manga - The OASG|https://www.theoasg.com/feed"
		"Manga - Twilights Cavern|https://twilightxcavern.wordpress.com/feed/"
		"Manga - Yen Press|https://yenpress.com/feed/"
		"Manga - Manga4Reader|https://www.manga4reader.com/feed/"
	)
	
	exitNewsMessage() {
		echo ""
		echo -e "$(tput bold)\033[0;30mExiting...\033[0m$(tput sgr0)" | lolcat -i
		echo ""
		exit
	}
	
	newsDivider() {
		echo ""
		printf "%*s\n" $(tput cols) | tr ' ' '~' | lolcat
		echo ""
	}
	
	displayNews() {
		local -a newsArr=("${(@f)$(curl -s "$1" | xmlstarlet sel -t -m '//item' -v "concat(position(), '. ', title)" -n -v link -n)}")
		
		echo -e "$(tput bold)\033[0;30mAccessing : $newsOption. ${sourceArr[1]} [$((${#newsArr[@]}/2)) items found]\033[0m$(tput sgr0)" | lolcat -i
		
		count=0
		
		for item in "${newsArr[@]}"
		do
			if (( count % 2 == 0 )); then
    				newsDivider
    				echo -e "$(tput bold)\033[0;30m$item\033[0m$(tput sgr0)" | sed 's/&amp;/\&/g;' | lolcat -f
  			else
  				echo ""
  				echo -e "\t\e[4mSource\e[24m ➡ $item" | sed 's/&amp;/\&/g;' | lolcat -f
			fi
			
			(( count++ ))
			
			# Prompt user to continue or go back to the menu after every 10th iteration
  			if (( count % 20 == 0 )); then
  				echo ""
  				printf "➡ Showing $((count/2))/$((${#newsArr[@]}/2)). Press enter to continue or type 'm' for menu, or 'q' to quit: " | lolcat -f
				read -r input
    				if [[ $input = "m" ]]; then
    					cmdArg=""
    					displayNewsMenu
    					break
    				elif [[ $input = "q" ]]; then
    					exitNewsMessage
				fi
  			fi
		done
		newsDivider
		echo -e "$(tput bold)\033[0;30mAll shown from : $newsOption. ${sourceArr[1]}. You can select others...\033[0m$(tput sgr0)" | lolcat -i
		newsDivider
		cmdArg=""
		displayNewsMenu
	}

	displayNewsMenu() { 
		if [[ "$cmdArg" == "" ]]; then
			echo ""
			echo "\e[4mSelect News\e[24m" | lolcat -f
			echo ""
  			for i in {1..${#newsSourcess[@]}}
			do
    				local -a sourceArr=("${(@s/|/)newsSourcess[$i]}")
    				echo "$i. ${sourceArr[1]}" | lolcat -f
  			done
  			echo ""
  			printf "➡ Please enter your option (1 is default, type 'q' to quit): " | lolcat -f
			read -r newsOption
			if [[ "$newsOption" == "q" ]]; then
				exitNewsMessage
			fi
		else
	    		newsOption="$cmdArg"
		fi
		
		if (( newsOption < 1 || newsOption > ${#newsSourcess[@]} )); then
    			newsOption=1
  		fi
  		local -a sourceArr=("${(@s/|/)newsSourcess[$newsOption]}")
  		echo ""
		displayNews "${sourceArr[2]}"
	}
	
	displayNewsMenu
}
```
**Step 3:** Save and exit the file and run the following command in your Terminal.  
```
source ~/.zshrc
```
**Step 4:** Call the news command from the terminal.  
```
news
```
