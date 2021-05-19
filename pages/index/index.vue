<template>
	<view class="load" v-if="page==-1">
		<view class="load-text">
			<view class="loaded-text">M</view>
			<view class="loading-text">USIC</view>
		</view>
	</view>
	<view v-else-if="page==0" class="main" id="view" :style="mybackground">
		.
		<view class="view" style="position:relative">
			
			<text class="title" style="left:3px;animation: blink1 20s ease infinite;">M</text>
			<text class="title" style="left:27px;animation: blink2 20s ease infinite;">U</text>
			<text class="title" style="left:48px;animation: blink3 20s ease infinite;">S</text>
			<text class="title" style="left:68px;animation: blink4 20s ease infinite;">I</text>
			<text class="title" style="left:77px;animation: blink5 20s ease infinite;">C</text>

			<view class="search" id="view2">
				<input class="searchText" v-model="searchForSongs" type="text" @confirm="find" confirm-type="search" placeholder="Search"
				 id="input" />
			</view>

			<image src="https://www.polihub.it/wp-content/uploads/2016/06/scouting.png" style="position:absolute;width:25px;height:25px;top:5px;right:5px;" @tap="find"></image>
			 
		</view>
		
		
		<view v-if="downloaded==true" style="position:fixed;top:45%;background-color:white">
			<text @click="openFile()">{{path}}</text>
		</view>
		
		<text v-if="downloading == true" style="position:fixed;top:45%;left:30%;background-color:white">
			DOWNLOADING
		</text>
		
		<view v-if="hasSearched == false && clickedLiked == false && clickedPlaylist == false && clikedHistory == false">
			<image :src="source" class="img" @tap="searchForImage"></image>
			<image src="https://img.icons8.com/clouds/2x/right.png" @tap="next_im" style="position:absolute;top:140px;right:5vw;height:50px;width:50px;"></image>
			<image src="https://img.icons8.com/clouds/452/long-arrow-left.png" @tap="prev_im" style="position:absolute;top:140px;left:5vw;height:50px;width:50px"></image>
			<image v-for="(circle,index) in img" :key="index" :src="img[index].circle" :style="img[index].style" :class="img[index].class"
			 @tap="switchImage(img[index].imgNum,imgcount)"></image>
		</view>
		<view v-if="hasSearched == false">
			<view v-if="clickedLiked == false && clickedPlaylist == false && clikedHistory == false" style="position:relative;width:250px;height:100px">
				<view @tap="clickedLiked=true;marginSize='margin-top:300px;margin-left:50px;width:90px;height:90px;margin-bottom:-8px;';random = false">
					<image src="https://4f15fi427agh15x4ol42ijp7-wpengine.netdna-ssl.com/wp-content/themes/vidyard-website/img/pages/developers/heart.png"
					 style="width:100px;margin-left:10px;height:100px"></image>
					<text style="position:absolute;left:35px;bottom:35px"> Liked\n Songs</text>
				</view>

				<view @tap="clickedPlaylist=true;marginSize='margin-top:300px;margin-left:50px;width:90px;height:90px;margin-bottom:-8px;';random = false">
					<image src="https://cdn2.iconfinder.com/data/icons/player-app-icons/512/playlist-512.png" style="position:absolute;top:0px;width:100px;left:100px;height:100px"></image>
					<text style="position:absolute;left:120px;bottom:0px;background-color:#000000;color:#FFFFFF;border-radius:5px;height:18px">
						Playlists</text>
				</view>
				<view @tap="getRandomSong()">
					<text v-if="random==false" style="position:absolute;left:120px;bottom:-50px;background-color:#000000;color:#FFFFFF;border-radius:5px;height:18px">
						random</text>
					<text v-else style="position:absolute;left:120px;bottom:-50px;background-color:#FFFFFF;color:#000000;border-radius:5px;height:18px">Skip</text>
				</view>

				<view @tap="clikedHistory=true;marginSize='margin-top:300px;margin-left:70px;width:90px;height:90px;margin-bottom:-8px;';random = false">
					<image src="https://cdn3.iconfinder.com/data/icons/google-material-design-icons/48/ic_history_48px-512.png" style="position:absolute;top:0px;width:100px;left:220px;height:100px"></image>
					<text style="position:absolute;left:315px;bottom:80px;color:#FFFFFF;height:18px"> H</text>
					<text style="position:absolute;left:320px;bottom:65px;color:#FFFFFF;height:18px"> I</text>
					<text style="position:absolute;left:318px;bottom:50px;color:#FFFFFF;height:18px"> S</text>
					<text style="position:absolute;left:318px;bottom:35px;color:#FFFFFF;height:18px"> T</text>
					<text style="position:absolute;left:315px;bottom:20px;color:#FFFFFF;height:18px"> O</text>
					<text style="position:absolute;left:318px;bottom:5px;color:#FFFFFF;height:18px"> R</text>
					<text style="position:absolute;left:318px;bottom:-10px;color:#FFFFFF;height:18px"> Y</text>
				</view>
			</view>
			<view v-if="random == true">
				<audio @play="addToHistory(randomSong[0],0)" @ended="getRandomSong()" style="position:absolute;top:70vh" controls="true" v-bind:src="randomSong[0].src" v-bind:name="randomSong[0].name" v-bind:poster="randomSong[0].poster" v-bind:author="randomSong[0].author"></audio>
				<view v-if="randomSong[0].liked == false" style="position:absolute;top:76vh;width:70px;height:22px;right:50px" @tap="likeSong(randomSong[0],index,0)">
					<image src="https://i0.wp.com/upload.wikimedia.org/wikipedia/commons/thumb/5/52/Heart_icon_red_hollow.svg/2000px-Heart_icon_red_hollow.svg.png" style="width:20px;height:20px"></image>
					<text>like</text>
				</view>
				
				<view v-else-if="randomSong[0].liked == true" style="position:absolute;top:76vh;width:70px;height:22px;right:50px" @tap="unlikeSong(randomSong[0],index,0,false)">
					<image src="https://4f15fi427agh15x4ol42ijp7-wpengine.netdna-ssl.com/wp-content/themes/vidyard-website/img/pages/developers/heart.png"
					 style="width:20px;height:20px"></image>
					<text>liked</text>
				</view>
			</view>
			<view v-if="clickedLiked == true">
				<text style="font-size:50px;margin-left:50px">LIKED:</text>
				<view v-for="(song,index) in likedSongs" :key='index' class="music" style="position:relative">
					<audio @play="addToHistory(song,index)" v-bind:src="likedSongs[index].src" controls="true" class="music" v-bind:name="likedSongs[index].name"
					 v-bind:author="likedSongs[index].author" v-bind:poster="likedSongs[index].poster"></audio>

					<view v-if="likedSongs[index].liked == true" style="position:absolute;border:1px solid #000000; width:70px;height:22px;background-color:#FFFFFF;border-radius:20px;left:3px"
					 @tap="unlikeSong(likedSongs[index],index,0,false)">
						<image src="https://4f15fi427agh15x4ol42ijp7-wpengine.netdna-ssl.com/wp-content/themes/vidyard-website/img/pages/developers/heart.png"
						 style="width:20px;height:20px"></image>
						<text>liked</text>
					</view>

					<view v-if="likedSongs[index].liked == false" style="position:absolute;border:1px solid #000000; width:70px;height:22px;background-color:#FFFFFF;border-radius:20px;left:3px"
					 @tap="likeSong(likedSongs[index],index,0)">
						<image src="https://i0.wp.com/upload.wikimedia.org/wikipedia/commons/thumb/5/52/Heart_icon_red_hollow.svg/2000px-Heart_icon_red_hollow.svg.png"
						 style="width:20px;height:20px"></image>
						<text>like</text>
					</view>

					<view style="position:absolute;border:1px solid #000000; width:130px;height:27px;background-color:#FFFFFF;border-radius:20px;left:80px"
					 @tap="downloadSong(likedSongs[index].src)">
						<image src="https://www.alnoorpk.com/Fiqh_Section/Baloogh-ul-Maram/fiqh_uz_zakaat/Zakaat%20Images/epaydwnld.png"
						 style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
						<text>download</text>
					</view>

					<view class="options" style="width:80px;height:24px;left:220px" @tap="addToPlaylistSong(likedSongs[index].src,index,likedSongs[index].place,0)">
						<image src="https://cdn2.iconfinder.com/data/icons/player-app-icons/512/playlist-512.png" style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
						<text>Add</text>
					</view>
					
					<view class="options" style="width:30px;height:30px;right:-100px;top:1;background-color:#1B4186;" @tap="showLyrics(song,index)">
						<text style="margin-left:10px;color:white">L</text>
					</view>
					
					<text v-if="song.showLyrics == true" style="background-color:white">
						\n\n
						{{song.lyrics}}
					</text>
					
					<view v-if="likedSongs[index].showPlaylists == true" style="background-color:#FFFFFF; width:100px;margin-left:200px;margin-top:40px;">
						<view v-for="(playlist,i) in playlist" :key="i" style="position:relative">
							<text @tap="addSongToPlaylist(i,likedSongs[index])">{{i+1}}. {{playlist.name}}</text>
							<text v-if="likedSongs[index].already == true" style="position:absolute;margin-top:20px;right:40px;background-color:#1EE7CD; height:40px;margin-left:-40px:margin-top:3px;border:1px solid #000000;white-space:nowrap;">\nalready
								in playlist</text>
						</view>
						<view v-if="playlist.length == 0">
							<text>
								No \n Playlists
							</text>
						</view>

						<view style="margin-left:20px;width:60px; height:20px; border:1px solid #000000; border-radius:20%;background-color:#5E18E7;margin-top:20px;"
						 @tap="showCreatePlaylist(index,likedSongs[index].place,0)">
							<text style="color:#FFFFFF"> Create</text>
						</view>
					</view>

					<view v-if="likedSongs[index].showPlaylists == true && likedSongs[index].clickedCreatePlaylist == true" style="position:absolute;right:70px;top:130px;width:170px;height:100px;background-color:#5E18E7;border:1px solid #000000">
						<input v-model="likedSongs[index].model" style="width:100px;height:30px;" type="text" placeholder="Name"
						 placeholder-style="color:#FFFFFF"></input>
						<input v-model="likedSongs[index].playlistPic" style="width:100px;height:30px;top:20px;" placeholder-style="color:#FFFFFF"
						 type="text" placeholder="Picture Link"></input>
						<image src="https://1it.ee/wp-content/uploads/2019/05/check-300x300.png" style="position:absolute;width:25px;height:25px;bottom:10px;right:5px;"
						 @tap="createPlaylist(likedSongs[index],0,0)"></image>
						<text v-if="likedSongs[index].error == true">
							Enter a Playlist\n Name
						</text>
					</view>
				</view>
			</view>
			<view v-if="clickedPlaylist == true">
				<text style="font-size:50px;margin-left:30px" v-if="showplaylist == false">PLAYLISTS:\n</text>
				<text style="font-size:30px;margin-left:50px" v-if="playlist.length == 0">No Playlists!</text>

				<view v-if="showplaylist == false" v-for="(playlist,index) in playlist" :key="index">
					<view style="position:relative;margin-top:10px;width:200px;height:100px;margin-left:20px;border:1px solid #000000;border-radius:10px;background-color:#FFFFFF; background-color:#BF40C9">
						<text>Playlist name: {{playlist.name}}</text>
						<image :src="playlist.picLink" style="width:200px;height:56px;margin-top:5px"></image>
						<image src="https://i3.wp.com/www.shareicon.net/data/2016/09/01/822390_delete_512x512.png" style="position:absolute;width:15px;height:15px;bottom:1px;left:5px"
						 @tap="deletePlaylist(index)"></image>
						<image src="http://217.199.187.192/boldonlinemarketing.com/wp-content/uploads/2015/04/design-and-development.png"
						 style="position:absolute;width:15px;height:15px;bottom:1px;left:30px" @tap="check(playlist)"></image>
						<image src="https://www.tynker.com/projects/images/daede7d49efef056591eab5f5e387bed180f3b90/image11---image1.png"
						 style="position:absolute;width:15px;height:15px;bottom:1px;left:55px" @tap="check2(playlist)"></image>
						<button @tap="openplaylist(index)" style="position:absolute;width:60px;height:15px;bottom:1px;right:20px;" type="text"></button>
						<text @tap="openplaylist(index)" style="position:absolute;bottom:-1px;right:24px;">
							OPEN
						</text>


						<view style="position:absolute;right:-90px;top:1px;width:80px;height:105px;margin-left:210px;border:1px solid #000000;border-radius:10px;background-color:#FFFFFF; background-color:#FFFFFF">
							<view style="width:73px;height:20px;background-color:#F89130;border-radius:10px;margin-top:3px;margin-left:2px;border:1px solid #000000;font-size:12px">{{playlist.songs.length+" "}}SONGS:</view>
							<view v-for="(songs,i) in playlist.songs" :key="i">
								<text v-if="playlist.songs[i].name.length<=6 && i<3" style="white-space:nowrap;">{{i+1}}.{{playlist.songs[i].name}}</text>
								<text v-if="playlist.songs[i].name.length>6 && i<3" style="white-space:nowrap;">{{i+1}}.{{playlist.songs[i].name[0]+playlist.songs[i].name[1]+playlist.songs[i].name[2]+playlist.songs[i].name[3]+playlist.songs[i].name[4]+playlist.songs[i].name[5]}}...
								</text>
								<text v-if="i==3" style="background-color:#841258;color:#000000;border-radius:20px;margin-left:5px;font-size:10px;margin-bottom:3px">
									+ {{playlist.songs.length-3}} more
								</text>
							</view>
						</view>
					</view>
					<view v-if="playlist.clickedRename == true" style="position:relative">
						<input v-model="playlist.newName" style="margin-left:20px;position:relative;margin-top:20px;width:200px;height:30px;background-color:#22D2D6;border-radius:10px;border:1px solid #000000"
						 placeholder="New Name" placeholder-style="color:#FFFFFF;margin-left:20px"><input>
						<image src="https://1it.ee/wp-content/uploads/2019/05/check-300x300.png" style="position:absolute;width:25px;height:25px;bottom:25px;right:60px;"
						 @tap="renamePlaylist(index)"></image>
					</view>

					<view v-if="playlist.clikedChangeImage == true" style="position:relative">
						<input v-model="playlist.newPic" style="margin-left:20px;position:relative;margin-top:20px;width:200px;height:30px;background-color:#22D2D6;border-radius:10px;border:1px solid #000000"
						 placeholder="New Image Link" placeholder-style="color:#FFFFFF;margin-left:20px"><input>
						<image src="https://1it.ee/wp-content/uploads/2019/05/check-300x300.png" style="position:absolute;width:25px;height:25px;bottom:25px;right:60px;"
						 @tap="changePlaylistimage(index)"></image>
					</view>
				</view>
				<view v-if="showplaylist == true" style="animation:colorchange 20s ease infinite;margin-left:20px;margin-top:20px;width:280px;border:1px solid #FFFFFF">
					<text style="font-size:50px;margin-left:30px">{{playlist[playlistNum].name}}:</text>
				</view>
				<view v-if="showplaylist == true" v-for="(song,index) in playlist[playlistNum].songs" :key='index' class="music"
				 style="position:relative">

					<audio @play="addToHistory(song,index)" v-bind:src="song.src" ref="playlistsongs" controls="true" class="music"
					 v-bind:name="song.name" v-bind:author="song.author" v-bind:poster="song.poster" @ended="playNextSong(index)"></audio>

					<view v-if="song.liked == true" style="position:absolute;border:1px solid #000000; width:70px;height:22px;background-color:#FFFFFF;border-radius:20px;left:3px"
					 @tap="unlikeSong(playlist[playlistNum].songs[index],playlistNum,index,false)">
						<image src="https://4f15fi427agh15x4ol42ijp7-wpengine.netdna-ssl.com/wp-content/themes/vidyard-website/img/pages/developers/heart.png"
						 style="width:20px;height:20px"></image>
						<text>liked</text>
					</view>

					<view v-if="song.liked == false" style="position:absolute;border:1px solid #000000; width:70px;height:22px;background-color:#FFFFFF;border-radius:20px;left:3px"
					 @tap="likeSong(playlist[playlistNum].songs[index],playlistNum,index)">
						<image src="https://i0.wp.com/upload.wikimedia.org/wikipedia/commons/thumb/5/52/Heart_icon_red_hollow.svg/2000px-Heart_icon_red_hollow.svg.png"
						 style="width:20px;height:20px"></image>
						<text>like</text>
					</view>

					<view style="position:absolute;border:1px solid #000000; width:130px;height:27px;background-color:#FFFFFF;border-radius:20px;left:80px"
					 @tap="downloadSong(playlist[playlistNum].songs[index].src)">
						<image src="https://www.alnoorpk.com/Fiqh_Section/Baloogh-ul-Maram/fiqh_uz_zakaat/Zakaat%20Images/epaydwnld.png"
						 style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
						<text>download</text>
					</view>

					<view class="options" style="width:80px;height:24px;left:220px" @tap="addToPlaylistSong(playlist[playlistNum].songs[index].src,playlistNum,playlist[playlistNum].songs[index].place,index)">
						<image src="https://cdn2.iconfinder.com/data/icons/player-app-icons/512/playlist-512.png" style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
						<text>Add</text>
					</view>

					<image src="https://cllrandrewjames.files.wordpress.com/2017/09/removed.png" style="position:absolute;right:-75px;width:20px;height:20px"
					 @tap="removeFromPlaylist(playlistNum,index)"></image>

					<view v-if="song.showPlaylists == true" style="background-color:#FFFFFF;width:100px;margin-left:200px;margin-top:40px;">
						<view v-for="(playlist,i) in playlist" :key="i">
							<text @tap="addSongToPlaylist(i,song)">{{i+1}}. {{playlist.name}}</text>
							<text v-if="song.already == true" style="position:absolute;margin-top:20px;right:20px;background-color:#1EE7CD; height:40px;margin-left:-40px:margin-top:3px;border:1px solid #000000;white-space:nowrap;">\nalready
								in playlist</text>
						</view>
						<view v-if="playlist.length == 0">
							<text>
								No \n Playlists
							</text>
						</view>
						<view style="width:60px; height:20px; border:1px solid #000000; border-radius:20%;background-color:#5E18E7;margin-top:20px;margin-left:20px"
						 @tap="showCreatePlaylist(playlistNum,playlist[playlistNum].songs[index].place,index)">
							<text style="color:#FFFFFF"> Create</text>
						</view>
					</view>
					<view v-if="song.showPlaylists == true && song.clickedCreatePlaylist == true" style="position:absolute;right:70px;top:130px;width:170px;height:100px;background-color:#5E18E7;border:1px solid #000000">
						<input v-model="playlist[playlistNum].songs[index].model" style="width:100px;height:30px;" type="text"
						 placeholder="Name" placeholder-style="color:#FFFFFF"></input>
						<input v-model="playlist[playlistNum].songs[index].playlistPic" style="width:100px;height:30px;top:20px;"
						 placeholder-style="color:#FFFFFF" type="text" placeholder="Picture Link"></input>
						<image src="https://1it.ee/wp-content/uploads/2019/05/check-300x300.png" style="position:absolute;width:25px;height:25px;bottom:10px;right:5px;"
						 @tap="createPlaylist(playlist[playlistNum].songs[index],playlistNum,index)"></image>
						<text v-if="playlist[playlistNum].songs[index].error == true">
							Enter a Playlist\n Name
						</text>
					</view>
				</view>
			</view>
			<view v-if="clikedHistory == true">
				<text style="font-size:50px;margin-left:50px">History:</text>
				<view v-for="(song,index) in History" :key="index" class="music" style="position:relative">
					<audio v-bind:src="song.src" controls="true" class="music" v-bind:name="song.name" v-bind:author="song.author"
					 v-bind:poster="song.poster"></audio>
					<view class="options" v-if="song.liked == true" style="width:70px;height:22px;left:3px" @tap="unlikeSong(song,index,0,true)">
						<image src="https://4f15fi427agh15x4ol42ijp7-wpengine.netdna-ssl.com/wp-content/themes/vidyard-website/img/pages/developers/heart.png"
						 style="width:20px;height:20px"></image>
						<text>liked</text>
					</view>
					<view class="options" v-else-if="song.liked == false" style="width:70px;height:22px;left:3px" @tap="likeSong(song,index,0)">
						<image src="https://i0.wp.com/upload.wikimedia.org/wikipedia/commons/thumb/5/52/Heart_icon_red_hollow.svg/2000px-Heart_icon_red_hollow.svg.png"
						 style="width:20px;height:20px"></image>
						<text>like</text>
					</view>

					<view class="options" style="width:130px;height:27px;left:80px" @tap="downloadSong(song.src)">
						<image src="https://www.alnoorpk.com/Fiqh_Section/Baloogh-ul-Maram/fiqh_uz_zakaat/Zakaat%20Images/epaydwnld.png"
						 style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
						<text>download</text>
					</view>

					<view class="options" style="width:80px;height:24px;left:220px" @tap="addToPlaylistSongs2(song,index)">
						<image src="https://cdn2.iconfinder.com/data/icons/player-app-icons/512/playlist-512.png" style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
						<text>Add</text>
					</view>

					<view v-if="song.showPlaylists == true" style="background-color:#FFFFFF; width:100px;margin-left:200px;margin-top:40px;">
						<view v-for="(playlist,i) in playlist" :key="i" style="postion:relative">
							<text @tap="addSongToPlaylist(i,song)">{{i+1}}. {{playlist.name}}</text>
							<text v-if="song.already == true" style="position:absolute;margin-top:20px;left:150px;background-color:#1EE7CD; height:40px;margin-left:-40px:margin-top:3px;border:1px solid #000000;white-space:nowrap;">\nalready
								in playlist</text>
						</view>

						<view v-if="playlist.length == 0">
							<text>
								No \n Playlists
							</text>
						</view>

						<view style="margin-left:20px;width:60px; height:20px; border:1px solid #000000; border-radius:20%;background-color:#5E18E7;margin-top:20px;"
						 @tap="showCreatePlaylist2(index,song.place,0)">
							<text style="color:#FFFFFF"> Create</text>
						</view>
					</view>

					<view v-if="song.showPlaylists == true && song.clickedCreatePlaylist == true" style="position:absolute;right:70px;top:130px;width:170px;height:100px;background-color:#5E18E7;border:1px solid #000000">
						<input v-model="song.model" style="width:100px;height:30px;" type="text" placeholder="Name" placeholder-style="color:#FFFFFF"></input>
						<input v-model="song.playlistPic" style="width:100px;height:30px;top:20px;" placeholder-style="color:#FFFFFF"
						 type="text" placeholder="Picture Link"></input>
						<image src="https://1it.ee/wp-content/uploads/2019/05/check-300x300.png" style="position:absolute;width:25px;height:25px;bottom:10px;right:5px;"
						 @tap="createPlaylist(song,0,0)"></image>
						<text v-if="song.error == true">
							Enter a Playlist\n Name
						</text>
					</view>
				</view>
			</view>
		</view>

		<text v-if="failed == true" style="font-size:40px"> Check your Internet Connection and try again</text>

		<view v-for="(song,index) in TheSongs" :key='index' class="music" style="position:relative;" :onLoad="checkIfLiked(TheSongs[index].id,index)">
			<audio v-bind:src="TheSongs[index].src" controls="true" class="music" v-bind:name="TheSongs[index].name"
			 v-bind:author="TheSongs[index].author" v-bind:poster="TheSongs[index].poster" @play="addToHistory(TheSongs[index])"></audio>
			<view class="options" v-if="TheSongs[index].liked == true" style="width:70px;height:22px;left:3px" @tap="unlikeSong(TheSongs[index],index,0,false)">
				<image src="https://4f15fi427agh15x4ol42ijp7-wpengine.netdna-ssl.com/wp-content/themes/vidyard-website/img/pages/developers/heart.png"
				 style="width:20px;height:20px"></image>
				<text>liked</text>
			</view>
			<view class="options" v-else-if="TheSongs[index].liked == false" style="width:70px;height:22px;left:3px" @tap="likeSong(TheSongs[index],index,0)">
				<image src="https://i0.wp.com/upload.wikimedia.org/wikipedia/commons/thumb/5/52/Heart_icon_red_hollow.svg/2000px-Heart_icon_red_hollow.svg.png"
				 style="width:20px;height:20px"></image>
				<text>like</text>
			</view>

			<view class="options" style="width:130px;height:27px;left:80px" @tap="downloadSong(TheSongs[index].src)">
				<image src="https://www.alnoorpk.com/Fiqh_Section/Baloogh-ul-Maram/fiqh_uz_zakaat/Zakaat%20Images/epaydwnld.png"
				 style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
				<text>download</text>
			</view>
			
			<view class="options" style="width:30px;height:30px;right:-100px;top:1;background-color:#1B4186;" @tap="showLyrics(TheSongs[index],index)">
				<text style="margin-left:10px;color:white">L</text>
			</view>
			
			<view class="options" style="width:80px;height:24px;left:220px" @tap="addToPlaylistSong(TheSongs[index].src,index,TheSongs[index].place,0)">
				<image src="https://cdn2.iconfinder.com/data/icons/player-app-icons/512/playlist-512.png" style="margin-left:5px;margin-top:3px;width:20px;height:20px"></image>
				<text>Add</text>
			</view>
			
			<text v-if="song.showLyrics == true" style="background-color:white">
				\n\n
				{{song.lyrics}}
			</text>

			<view v-if="TheSongs[index].showPlaylists == true" style="background-color:#FFFFFF; width:100px;margin-left:200px;margin-top:40px;">
				
				<view v-for="(playlist,i) in playlist" :key="i" style="postion:relative">
				<text @tap="addSongToPlaylist(i,TheSongs[index])">{{i+1}}. {{playlist.name}}</text>
				<text v-if="TheSongs[index].already == true" style="position:absolute;margin-top:20px;left:150px;background-color:#1EE7CD; height:40px;margin-left:-40px:margin-top:3px;border:1px solid #000000;white-space:nowrap;">\nalready
					in playlist</text>
				</view>
				

				<view v-if="playlist.length == 0">
					<text>
						No \n Playlists
					</text>
				</view>

				<view style="margin-left:20px;width:60px; height:20px; border:1px solid #000000; border-radius:20%;background-color:#5E18E7;margin-top:20px;"
				 @tap="showCreatePlaylist(index,TheSongs[index].place,0)">
					<text style="color:#FFFFFF"> Create</text>
				</view>
			</view>

			<view v-if="TheSongs[index].showPlaylists == true && TheSongs[index].clickedCreatePlaylist == true" style="position:absolute;right:70px;top:130px;width:170px;height:100px;background-color:#5E18E7;border:1px solid #000000">
				<input v-model="TheSongs[index].model" style="width:100px;height:30px;" type="text" placeholder="Name"
				 placeholder-style="color:#FFFFFF"></input>
				<input v-model="TheSongs[index].playlistPic" style="width:100px;height:30px;top:20px;" placeholder-style="color:#FFFFFF"
				 type="text" placeholder="Picture Link"></input>
				<image src="https://1it.ee/wp-content/uploads/2019/05/check-300x300.png" style="position:absolute;width:25px;height:25px;bottom:10px;right:5px;"
				 @tap="createPlaylist(TheSongs[index],0,0)"></image>
				<text v-if="TheSongs[index].error == true">
					Enter a Playlist\n Name
				</text>
			</view>
		</view>

		<view class="footer">
			<image src="https://img.icons8.com/bubbles/2x/music.png" style="position:fixed;bottom:-6px;left:60px;width:70px;height:70px"
			 @tap="music"></image>
			<image src="https://image.flaticon.com/icons/png/512/471/471012.png" style="position:fixed;right:60px;width:55px;height:55px;bottom:2px;"
			 @tap="explore"></image>
			<image src="https://andrify.net/images/settings-icon.png" style="position:fixed;width:20px;height:20px;right:5px;bottom:2px"
			 @tap="showSettings"></image>

			<view style="position:fixed;bottom:65px" v-if="settings==true">
				<input id="back" style="width:250px;background-color:#FFFFFF;margin-left:0px; border: 1px solid #000000;border-radius:10px;"
				 placeholder="Enter Background Link" @confirm="setbackground" type="text" confirm-type="done"></input>
				<button size="mini" style="position:fixed;right:5px;bottom:65px;width:60px;height:25px" @tap="imageChange">Auto</button>
			</view>

			<view>
				<text style="position:fixed;bottom:48px;right:64px;font-size:8px">E</text>
				<text style="position:fixed;bottom:41px;right:57px;font-size:8px">X</text>
				<text style="position:fixed;bottom:31px;right:54px;font-size:8px">P</text>
				<text style="position:fixed;bottom:23px;right:54px;font-size:8px">L</text>
				<text style="position:fixed;bottom:14px;right:53px;font-size:8px">O</text>
				<text style="position:fixed;bottom:7px;right:57px;font-size:8px">R</text>
				<text style="position:fixed;bottom:0px;right:64px;font-size:8px">E</text>
			</view>

			<view>
				<text style="color:#FFFFFF;position:fixed;bottom:48px;left:60px;font-size:10px">M</text>
				<text style="color:#FFFFFF;position:fixed;bottom:37px;left:60px;font-size:10px">U</text>
				<text style="color:#FFFFFF;position:fixed;bottom:24px;left:60px;font-size:10px">S</text>
				<text style="color:#FFFFFF;position:fixed;bottom:12px;left:60px;font-size:10px">I</text>
				<text style="color:#FFFFFF;position:fixed;bottom:0px;left:60px;font-size:10px">C</text>
			</view>
		</view>
	</view>

	<view v-else class="main" :style="mybackground">
		.
		
		<view v-if="showMV == false" style="position:relative">
			<image  @tap="showMusicVideo()" src="https://tandsgo.com/wp-content/uploads/2018/01/portfolio-icon-onlinevideos.png" style="height:100px;width:100px;margin-left:50px;margin-top:50px;"></image>
			<text style="position:absolute;font-size:30px;left:75px;background-color:#FFFFFF;border-radius:15px;border:1px solid #000000;bottom:-30px;">MV</text>
		
			<view>
			
					<view style="width:100%;position:absolute;top:250px;background:white">
						Mr.PinkLemonade:
						<div v-for="(quotes,index) in robotText" :key="index">
							<view>{{robotText[index].english}}</view>
							<view>{{robotText[index].chinese}}</view>
						</div>
					</view>
				
				
				<image @click="chat" src="http://www.myiconfinder.com/uploads/iconsets/f269bbc2a3bd8805b0dccb6c36dd2fac.png" style="position:absolute;left:140px;top:225px;height:30px;width:30px"></image>
			</view>
			
		</view>
		
		<view v-else v-for="(post,index) in posts" :key="index" style="margin-top:20px;margin-left:20px;">
			<video v-bind:src="post.url"></video>
		</view>
		
		
		<view class="footer">
			<image src="https://img.icons8.com/bubbles/2x/music.png" style="position:fixed;bottom:-6px;left:60px;width:70px;height:70px"
			 @tap="music"></image>
			<image src="https://image.flaticon.com/icons/png/512/471/471012.png" style="position:fixed;right:60px;width:55px;height:55px;bottom:2px;"
			 @tap="explore"></image>
			<image src="https://andrify.net/images/settings-icon.png" style="position:fixed;width:20px;height:20px;right:5px;bottom:2px"
			 @tap="showSettings"></image>

			<view style="position:fixed;bottom:65px" v-if="settings==true">
				<input id="back" style="width:250px;background-color:#FFFFFF;margin-left:0px; border: 1px solid #000000;border-radius:10px;"
				 placeholder="Enter Background Link" @confirm="setbackground" type="text" confirm-type="done"></input>
				<button size="mini" style="position:fixed;right:5px;bottom:65px;width:60px;height:25px" @tap="imageChange">Auto</button>
			</view>
		</view>

		<view>
			<text style="color:#FFFFFF;position:fixed;bottom:48px;right:64px;font-size:8px">E</text>
			<text style="color:#FFFFFF;position:fixed;bottom:41px;right:57px;font-size:8px">X</text>
			<text style="color:#FFFFFF;position:fixed;bottom:31px;right:54px;font-size:8px">P</text>
			<text style="color:#FFFFFF;position:fixed;bottom:23px;right:54px;font-size:8px">L</text>
			<text style="color:#FFFFFF;position:fixed;bottom:14px;right:53px;font-size:8px">O</text>
			<text style="color:#FFFFFF;position:fixed;bottom:7px;right:57px;font-size:8px">R</text>
			<text style="color:#FFFFFF;position:fixed;bottom:0px;right:64px;font-size:8px">E</text>
		</view>

		<view>
			<text style="position:fixed;bottom:48px;left:60px;font-size:10px">M</text>
			<text style="position:fixed;bottom:37px;left:60px;font-size:10px">U</text>
			<text style="position:fixed;bottom:24px;left:60px;font-size:10px">S</text>
			<text style="position:fixed;bottom:12px;left:60px;font-size:10px">I</text>
			<text style="position:fixed;bottom:0px;left:60px;font-size:10px">C</text>
		</view>
	</view>
</template>


<script>
	export default {
		name: 'Page1',
		data() {
			return {
				imgcount: 0,
				img: [{
					src: "https://www.monstersandcritics.com/wp-content/uploads/2019/05/Carole-And-Tuesday-Season-2-release-date-Carole-Tuesday-Part-2-for-Netflix-English-dub-sub.jpg",
					search: "Carole & Tuesday",
					imgNum: 0,
					style: "left:60px;",
					circle: "http://artpointgallery5.com/images/circle-clipart-4.png",
					class: "blueCircle"
				}, {

					src: "https://m.media-amazon.com/images/M/MV5BMTQxMjljYjMtNjk0Yi00MWFiLTg2YTUtMmVhZjMzM2FhNjcxXkEyXkFqcGdeQXVyNDQxNjcxNQ@@._V1_.jpg",
					search: "菅田将暉",
					circle: "https://static.thenounproject.com/png/1625516-200.png",
					style: "",
					imgNum: 1,
					style: "left:100px;",
					class: "emptyCircle"
				}, {

					src: "https://lastfm-img2.akamaized.net/i/u/770x0/3c1531c4d235dbcd6a36b86a1f344d36.jpg",
					search: "Aimer",
					circle: "https://static.thenounproject.com/png/1625516-200.png",
					imgNum: 2,
					style: "left:140px;",
					class: "emptyCircle"
				}, {

					src: "https://www.irishtimes.com/polopoly_fs/1.3250904.1507642347!/image/image.jpg_gen/derivatives/box_620_330/image.jpg",
					search: "Ariana Grande",
					circle: "https://static.thenounproject.com/png/1625516-200.png",
					imgNum: 3,
					style: "left:180px;",
					class: "emptyCircle"
				}, {

					src: "https://img1.ak.crunchyroll.com/i/spire4/ca97490a4098e3e32da87a561e3015491550621552_full.jpg",
					search: "Watashi ni tenshi ga maiorita",
					circle: "https://static.thenounproject.com/png/1625516-200.png",
					imgNum: 4,
					style: "left:220px;",
					class: "emptyCircle"
				}, {

					src: "https://www.elle.com.hk/var/ellehk/storage/images/celebrity/feature/g.e.m.-boyfriend/25066513-1-chi-HK/G.E.M._img_885_590.png",
					search: "邓紫棋",
					circle: "https://static.thenounproject.com/png/1625516-200.png",
					imgNum: 5,
					style: "left:260px;",
					class: "emptyCircle",
				}],
				source: "https://www.monstersandcritics.com/wp-content/uploads/2019/05/Carole-And-Tuesday-Season-2-release-date-Carole-Tuesday-Part-2-for-Netflix-English-dub-sub.jpg",
				mybackground: "background-image:url(https://www.pixel-creation.com/wp-content/uploads/tokyo-street-nightarsenixc-scape-art-pinterest-tokyo-and-800x800.jpg);height:130vh",
				count2: 0,
				AutoChange: null,
				searchForSongs: "",
				search: null,
				CanSearch: null,
				TheSongs: [],
				hasSearched: false,
				likedSongs: [],
				settings: false,
				clickedLiked: false,
				clickedPlaylist: false,
				playlist: [],
				noPlaylist: true,
				failed: false,
				showplaylist: false,
				playlistNum: 0,
				backgroundLink: "https://www.pixel-creation.com/wp-content/uploads/tokyo-street-nightarsenixc-scape-art-pinterest-tokyo-and-800x800.jpg",
				padditionalHeight: 0,
				ladditionHeight: 0,
				posts: [],
				page: -1,
				clikedHistory: false,
				History: [],
				randomSong:[],
				random:false,
				showMV:false,
				tracknumber:0,
				downloaded:false,
				path:"",
				downloading:false,
				robotText:[]
			}
		},

		onLoad() {
			
		},

		methods: {
			next_im() {
				let currentImage = this.imgcount;
				this.imgcount++;
				console.log("next image");
				if (this.imgcount >= this.img.length) {
					this.imgcount = 0;
				}
				this.source = this.img[this.imgcount].src;
				this.switchImage(this.imgcount, currentImage);
			},
			prev_im() {
				let currentImage = this.imgcount;
				this.imgcount--;
				console.log("previous image");
				if (this.imgcount <= -1) {
					this.imgcount = this.img.length - 1;
				}
				this.source = this.img[this.imgcount].src;
				this.switchImage(this.imgcount, currentImage);
			},
			find() {
				this.mybackground = "background-image:url(" + this.backgroundLink + ");height:";
				this.hasSearched = true;
				console.log("finding songs for - " + this.searchForSongs + "....");
				this.CanSearch = "'" + this.searchForSongs + "'";

				for (let i = 0; i < this.likedSongs.length; i++) {
					if (this.likedSongs[i].liked == false) {
						this.likedSongs.splice(i, 1)
						i--;
					}
				};

				uni.request({
					url: 'https://codingfree.cn:3000/search',
					data: {
						keywords: this.CanSearch
					},
					success: (res) => {
						this.failed = false;
						console.log(res.data.result.songs.length + " songs found");
						console.log("songs are loading..");
						this.TheSongs = [];
						for (let i = 0; i < res.data.result.songs.length; i++) {
							let songName = res.data.result.songs[i].name;
							let songAuthor = res.data.result.songs[i].artists[0].name;
							let songPoster = res.data.result.songs[i].artists[0].img1v1Url;
							let songId = res.data.result.songs[i].id;
							let lyrics;

							uni.request({
								url:'https://codingfree.cn:3000/lyric?',
								data: {
									id: songId
								},
								success: (res) => {
									lyrics = res.data.lrc.lyric;
								}
							})
							
							uni.request({
								url: 'https://codingfree.cn:3000/song/url',
								data: {
									id: songId
								},
								success: (res) => {
									if (res.data.data[0].url) {
										this.TheSongs.push({
											src: res.data.data[0].url,
											name: songName,
											author: songAuthor,
											poster: songPoster,
											id: songId,
											liked: false,
											place: "TheSongs",
											showPlaylists: false,
											clickedCreatePlaylist: false,
											model: "",
											playlistPic: "",
											error: false,
											already: false,
											lyrics:lyrics,
											showLyrics:false
										})
									}
									this.marginSize = 'margin-top:0px;margin-left:50px;width:90px;height:90px;margin-bottom:-8px;';
								},
							});
						};
					},
					fail: (res) => {
						this.failed = true;
					}
				});
			},
			setbackground(myback) {
				this.backgroundLink = myback.target.value;
				if (this.hasSearched == false) {
					this.mybackground = "background-image:url(" + this.backgroundLink + ");height:130vh";
				} else {
					this.mybackground = "background-image:url(" + this.backgroundLink + ");height:";
				}

				console.log("changing background...");
				console.log(myback.target.value);

			},
			imageChange() {
				let self = this;
				this.count2++;
				if (this.count2 > 1) {
					this.count2 = 0;
				}
				if (this.count2 == 0) {
					console.log("Automatic Random Image Change has been turned on");
					this.AutoChange = setInterval(function() {
						let currentImage = self.imgcount;
						let a = Math.floor(Math.random() * self.img.length);
						self.imgcount = a;
						self.source = self.img[a].src;
						self.switchImage(self.imgcount, currentImage)
					}, 10000);
				} else {
					clearInterval(this.AutoChange);
					console.log("Automatic Random Image Change has been turned off");
				}
			},
			music() {
				console.log("Switching to Music Page...");
				this.TheSongs = [];
				this.hasSearched = false;
				for (let i = 0; i < this.likedSongs.length; i++) {
					if (this.likedSongs[i].liked == false) {
						this.likedSongs.splice(i, 1)
						i--;
					}
				};
				this.clickedLiked = false;
				this.clickedPlaylist = false;
				this.failed = false;
				this.clikedHistory = false;
				this.searchForSongs = "";
				this.showplaylist = false;
				this.mybackground = "background-image:url(" + this.backgroundLink + ");height:130vh";
				this.page = 0;
				this.random=false;
			},
			explore() {
				let self = this;
				console.log("Switching to Explore Page..., Auto Image Change Paused");
				clearInterval(this.AutoChange);
				this.page = 1;
				this.mybackground = "background-image:url(" + this.backgroundLink + ");height:130vh";
				this.FindMv();
				this.showMV = false;
				this.robotText = [];
			},
			likeSong(song, index, i) {
				if (song.place == "likedSongs") {
					console.log("Song liked, name:" + song.name + " by:" + song.author);
					song.liked = true;
				} else if (song.place == "TheSongs") {
					console.log("Song liked, name:" + song.name + " by:" + song.author);
					console.log(song.src)
					song.liked = true;

					this.likedSongs.push({
						src: song.src,
						name: song.name,
						author: song.author,
						poster: song.poster,
						id: song.id,
						liked: true,
						place: "likedSongs",
						showPlaylists: false,
						clickedCreatePlaylist: false,
						model: "",
						playlistPic: "",
						error: false,
						already: false,
						lyrics:song.lyrics,
						showLyrics:false
					})
				} else if (song.place == "playlist") {
					console.log("Song liked, name:" + song.name + " by:" + song.author);
					console.log(song.src)
					this.playlist[index].songs[i].liked = true;
					this.likedSongs.push({
						src: song.src,
						name: song.name,
						author: song.author,
						poster: song.poster,
						id: song.id,
						liked: true,
						place: "likedSongs",
						showPlaylists: false,
						clickedCreatePlaylist: false,
						model: "",
						playlistPic: "",
						error: false,
						lyrics:song.lyrics,
						showLyrics:false
					})
				}
				 else if (song.place == "random") {
					console.log("Song liked, name:" + song.name + " by:" + song.author);
					this.randomSong[0].liked = true;
					console.log(song.src)
					this.likedSongs.push({
						src: song.src,
						name: song.name,
						author: song.author,
						poster: song.poster,
						id: song.id,
						liked: true,
						place: "likedSongs",
						showPlaylists: false,
						clickedCreatePlaylist: false,
						model: "",
						playlistPic: "",
						error: false,
						lyrics:song.lyrics,
						showLyrics:false
					})
				}
				this.ladditionHeight++;
			},
			downloadSong(url) {
				console.log("downloading song:" + url + "...");
				let self = this;
				this.downloading = true;
				setTimeout(function() {
					self.downloading = false;
				}, 5000);
				
				const downloadTask = uni.downloadFile({
					url: url,
					success: (res) => {
						uni.saveFile({
							tempFilePath: res.tempFilePath,
							success: function(res) {
								var savedFilePath = res.savedFilePath;
								console.log('download successful!');
								console.log("file path:" + savedFilePath);
								self.path = savedFilePath;
								self.downloaded = true;
								setTimeout(function() {
									self.downloaded = false;
								}, 10000);
							}
						});
					}
				})
				downloadTask.onProgressUpdate((res) => {
					console.log('Downloaded:' + res.totalBytesWritten + "/" + res.totalBytesExpectedToWrite);

					if (res.progress > 50) {
						downloadTask.abort();
					}
				});
			},
			unlikeSong(song, index, idex2, history) {
				if (song.place == "likedSongs") {
					console.log("Song unliked - " + song.name + ", by: " + song.author)
					song.liked = false;
				} else {
					console.log("Song unliked - " + song.name + ", by: " + song.author);
					for (let i = 0; i < this.likedSongs.length; i++) {
						if (this.likedSongs[i].id == song.id) {
							this.likedSongs.splice(i, 1);
							song.liked = false;
						}
					}
					for (let i = 0; i < this.history.length; i++) {
						if (this.history[i].id == song.id) {
							history[i].liked = false;
						}
					}
				
				}
				this.ladditionHeight--;
			},
			searchForImage() {
				this.searchForSongs = this.img[this.imgcount].search,
					this.find();
			},
			switchImage(num, currentImage) {
				this.imgcount = num;
				this.source = this.img[this.imgcount].src;
				this.img[currentImage].circle = "https://static.thenounproject.com/png/1625516-200.png";
				this.img[currentImage].class = "emptyCircle";
				this.img[this.imgcount].circle = "http://artpointgallery5.com/images/circle-clipart-4.png";
				this.img[this.imgcount].class = "blueCircle";
			},
			showSettings() {
				if (this.settings == true) {
					this.settings = false;
				} else if (this.settings == false) {
					this.settings = true;
				}
			},
			checkIfLiked(id, index) {
				for (let i = 0; i < this.likedSongs.length; i++) {
					if (id == this.likedSongs[i].id) {
						this.TheSongs[index].liked = true;
					}
				}
			},
			addToPlaylistSong(url, index, place, i) {
				if (place == "TheSongs") {
					if (this.TheSongs[index].showPlaylists == true) {
						this.TheSongs[index].showPlaylists = false;
					} else {
						this.TheSongs[index].showPlaylists = true;
					}
				} else if (place == "likedSongs") {
					if (this.likedSongs[index].showPlaylists == true) {
						this.likedSongs[index].showPlaylists = false;
					} else {
						this.likedSongs[index].showPlaylists = true;
					}
				} else if (place == "playlist") {
					if (this.playlist[index].songs[i].showPlaylists == true) {
						this.playlist[index].songs[i].showPlaylists = false;
					} else {
						this.playlist[index].songs[i].showPlaylists = true;
					}
				}

			},
			createPlaylist(song, index, i) {
				if (song.place == "TheSongs" || song.place == "likedSongs" || song.place == "random") {
					if (song.model == "") {
						song.error = true;
						setTimeout(function() {
							song.error = false;
						}, 1000);

						console.log("error: no name");
					} else {
						console.log("creating playlist: " + song.model + ". picture: " + song.playlistPic);
						this.playlist.push({
							name: song.model,
							picLink: song.playlistPic,
							songs: [{
								src: song.src,
								name: song.name,
								author: song.author,
								poster: song.poster,
								id: song.id,
								liked: song.liked,
								place: "playlist",
								showPlaylists: false,
								clickedCreatePlaylist: false,
								model: "",
								playlistPic: "",
								error: false,
								already: false
							}],
							clickedRename: false,
							clikedChangeImage: false,
							newName: "",
							newPic: "",
						});
						song.clickedCreatePlaylist = false;
						song.model = "";
						song.playlistPic = "";
						this.noPlaylist = false;
					}
				} 
				else if(song.place=="playlist")  {
					if (this.playlist[index].songs[i].model == "") {
						this.playlist[index].songs[i].error = true;
						setTimeout(function() {
							this.playlist[index].songs[i].error = false;
						}, 1000);

						console.log("error: no name");
					} else {
						console.log("creating playlist: " + this.playlist[index].songs[i].model + ". picture: " + this.playlist[index].songs[
							i].playlistPic);
						this.playlist.push({
							name: this.playlist[index].songs[i].model,
							picLink: this.playlist[index].songs[i].playlistPic,
							songs: [{
								src: this.playlist[index].songs[i].src,
								name: this.playlist[index].songs[i].name,
								author: this.playlist[index].songs[i].author,
								poster: this.playlist[index].songs[i].poster,
								id: this.playlist[index].songs[i].id,
								liked: this.playlist[index].songs[i].liked,
								place: "playlist",
								showPlaylists: false,
								clickedCreatePlaylist: false,
								model: "",
								playlistPic: "",
								error: false
							}],
							clickedRename: false,
							clikedChangeImage: false,
							newName: "",
							newPic: "",
						});
						this.playlist[index].songs[i].clickedCreatePlaylist = false;
						this.playlist[index].songs[i].model = "";
						this.playlist[index].songs[i].playlistPic = "";
						this.noPlaylist = false;
					}
				}
				this.padditionalHeight++;
			},
			showCreatePlaylist(index, place, i) {
				if (place == "TheSongs") {
					if (this.TheSongs[index].clickedCreatePlaylist == false) {
						this.TheSongs[index].clickedCreatePlaylist = true;
					} else {
						this.TheSongs[index].clickedCreatePlaylist = false;
					}
				} else if (place == "likedSongs") {
					if (this.likedSongs[index].clickedCreatePlaylist == true) {
						this.likedSongs[index].clickedCreatePlaylist = false;
					} else {
						this.likedSongs[index].clickedCreatePlaylist = true;
					}
				} else if (place == "playlist") {
					if (this.playlist[index].songs[i].clickedCreatePlaylist == true) {
						this.playlist[index].songs[i].clickedCreatePlaylist = false;
					} else {
						this.playlist[index].songs[i].clickedCreatePlaylist = true;
					}
				}
			},
			addSongToPlaylist(index, song) {
				let iqwe = false;
				for (let i = 0; i < this.playlist[index].songs.length; i++) {
					if (song.id == this.playlist[index].songs[i].id) {
						console.log("already in playlist!");
						iqwe = true;
						song.already = true;
						setTimeout(function() {
							song.already = false;
						}, 1000);
					}
				}
				if (iqwe == false) {
					console.log(song.name + " added to playlist: " + this.playlist[index].name + ". There are " + (this.playlist[index]
						.songs.length + 1) + " in this playlist now");
					this.playlist[index].songs.push({
						src: song.src,
						name: song.name,
						author: song.author,
						poster: song.poster,
						id: song.id,
						liked: song.liked,
						place: "playlist",
						showPlaylists: false,
						clickedCreatePlaylist: false,
						model: "",
						playlistPic: "",
						error: false
					});
				}
			},
			deletePlaylist(index) {
				console.log(this.playlist[index].name + " has been deleted");
				this.playlist.splice(index, 1)
			},
			renamePlaylist(index) {
				this.playlist[index].name = this.playlist[index].newName;
				this.playlist[index].newName = "";
			},
			changePlaylistimage(index) {
				this.playlist[index].picLink = this.playlist[index].newPic;
				this.playlist[index].newPic = "";
			},
			check(playlist) {
				if (playlist.clickedRename == true) {
					playlist.clickedRename = false
				} else {
					playlist.clickedRename = true
				}
			},
			check2(playlist) {
				if (playlist.clikedChangeImage == true) {
					playlist.clikedChangeImage = false
				} else {
					playlist.clikedChangeImage = true
				}
			},
			openplaylist(index) {
				this.showplaylist = true;
				this.playlistNum = index;
			},
			removeFromPlaylist(playlistnumber, index) {
				console.log(this.playlist[playlistnumber].songs[index].name + " has been romoved from the playlist");
				this.playlist[playlistnumber].songs.splice(index, 1);

			},
			paddHeight() {
				let product = 0;
				product = this.padditionalHeight * 50;
				product = product + 130;
				this.mybackground = "background-image:url(" + this.backgroundLink + ");height:" + product + "vh";
			},
			laddHeight() {
				let product = 0;
				product = this.ladditionalHeight * 50;
				product = product + 130;
				this.mybackground = "background-image:url(" + this.backgroundLink + ");height:" + product + "vh";
			},
			addToHistory(song,index) {
				this.tracknumber = index;
				let alreadyIn = false;
				for (let i=0;i<this.History.length;i++)
				{
					if (song.id == this.History[i].id)
					{
						alreadyIn = true;
					}
				
				}
				
				if (alreadyIn == false)
				{
					
					this.History.push(song);
				}	
			},
			addToPlaylistSongs2(song, index) {
				if (this.History[index].showPlaylists == true) {
					this.History[index].showPlaylists = false;
				} else {
					this.History[index].showPlaylists = true;
				}
			},
			showCreatePlaylist2(index, place, i) {
				if (this.History[index].clickedCreatePlaylist == false) {
					this.History[index].clickedCreatePlaylist = true;
				} else {
					this.History[index].clickedCreatePlaylist = false;
				}
			},
			getRandomSong()
			{
				this.random = true;
				const max = 999999999;
				const min = 1000000;
				let valid = false;
				this.randomSong = [];
				let randomNumber = (Math.floor(Math.random()*(max-min+1)+min));
				uni.showLoading({
					title:'FINDING RANDOM SONG...'
				});
				uni.request({
					url: 'https://codingfree.cn:3000/song/url',
					data: {
						id: randomNumber
					},
					success: (res) => {
						
						if (res.data.data[0].url == null)
						{
							console.log(randomNumber +" is null");
							this.getRandomSong();
						}
						else
						{
							
							let source = res.data.data[0].url;
							let lyrics;
							
							uni.request({
								url: 'https://codingfree.cn:3000/search',
								data: {
									keywords: res.data.data[0].id
								},
								success: (res) => {
									console.log(randomNumber +" - success");
									console.log(res.data.result.songs);
									if(res.data.result.songCount >0)
									{
										if(res.data.result.songs[0].name == 'null')
										{
											this.getRandomSong();
										}
										else
										{
											let songName = res.data.result.songs[0].name;
											let songAuthor = res.data.result.songs[0].artists[0].name;
											let songPoster = res.data.result.songs[0].artists[0].img1v1Url;
											let songId = res.data.result.songs[0].id;
											uni.request({
												url:'https://codingfree.cn:3000/lyric?',
												data: {
													id: songId
												},
												success: (res) => {
													if (res.data.uncollected == true)
													{
														lyrics = "Can't find Lyrics";
													}
													else{
														lyrics = res.data.lrc.lyric;
													}
													
														this.randomSong.push({
														src:source,
														name:songName,
														author:songAuthor,
														poster:songPoster,
														id: songId,
														liked: false,
														showPlaylists: false,
														clickedCreatePlaylist: false,
														model: "",
														playlistPic: "",
														error: false,
														place:"random",
														lyrics:lyrics,
														showLyrics:false
													});
													console.log(this.randomSong);
													 uni.hideLoading();
												}
											})
										}
									}
									else
									{
										console.log("Can't find song");
										this.getRandomSong();
									}
								}
							})
						}
					}
					});
			},
			FindMv()
			{
				this.posts = [];
				uni.request({
				url: 'https://codingfree.cn:3000/top/mv',
				data: {
					
				},
				success: (res) => {
					for (let i =0; i<res.data.data.length;i++)
					{
						uni.request({
						url: 'https://codingfree.cn:3000/mv/url?',
						data: {
							id:res.data.data[i].id
						},
						success: (res) => {
							
							this.posts.push(res.data.data);
						}
						})	
					}
				}
				})
			},
			showMusicVideo()
			{
				this.showMV = true;
				this.mybackground = "background-image:url(" + this.backgroundLink + ");height:";
			},
			openFile()
			{
				
			},
			playAudio()
			{
				if (music.paused) {
				music.play();
				pButton.className = "";
				pButton.className = "pause";
			  } else {
				music.pause();
				pButton.className = "";
				pButton.className = "play";
			  }
			},
			showLyrics(song,index)
			{
				if (song.showLyrics == false)
				{
					song.showLyrics = true;
				}
				else
				{
					song.showLyrics = false;
				}
			},
			chat()
			{
				const parm = 1;
				uni.showLoading({
					title: 'LOADING...'
				});
				uni.request({
					url: 'https://route.showapi.com/1211-1',
					data: {
						showapi_appid:'100753',
						showapi_sign:'71d401d4bcfc4945bce26d4fbf41a204',
						count:parm,
						
					},
					success: (res) => {
						console.log(res.data.showapi_res_body.data);
						// this.robotText = res.data.showapi_res_body.text;
						let a = res.data.showapi_res_body.data;
						this.robotText = a;
						uni.hideLoading();
						console.log("MR.PINKLEMONADE : "+this.robotText[0].english)
						console.log(a[0].chinese)
					}
				});
			
			}
			
		},
		created() {
			let self = this;
			console.log("Automatic Random Image Change Every 10 Seconds");
			this.AutoChange = setInterval(function() {
				let currentImage = self.imgcount;
				let a = Math.floor(Math.random() * self.img.length);
				self.imgcount = a;
				self.source = self.img[a].src;
				self.switchImage(self.imgcount, currentImage)
			}, 10000);
			setTimeout(function() {
				self.page = 0;
			}, 5000);
		},
	}
</script>

<style>
	.view {
		margin-top: 0px;
		height: 5.5vh;
		width: 95vw;
		background-color: #F5B4B4;
		border-radius: 10px;
		margin-left: 10px;
		border: 1px solid #000000;
	}

	.main {
		width: 100%;
		position: relative;
	}

	.search {
		margin-left: 100px;
		margin-top: 5px;
		width: 58vw;
		border: 1px solid #3F3F3F;
		border-radius: 10px;
		text-indent: 70px;
		padding: 0.3px;
		background: #F0E8E8;
	}

	.img {
		width: 93vw;
		height: 53vw;
		border-radius: 10%;
		margin-top: 15px;
		margin-left: 10px;
		display: this.show2;
		border: solid 1px #000000;
	}

	.music {
		margin-left: 3vw;
		margin-top: 20px;
		width: 250px;
	}

	.searchText {
		margin-left: 1px;
		width: 180px;
	}

	.title {
		position: absolute;
		top: 0px;
		font-size: 30px;
		margin-top: 1px;
		font-weight: bold;
		font-family: Arial;
	}

	@keyframes blink1 {
		0% {
			color: #FFFFFF;
		}

		10% {
			color: #000000;
		}

		80% {
			color: #000000;
		}

		90% {
			color: #FFFFFF;
		}

		100% {
			color: #FFFFFF;
		}
	}

	@keyframes blink2 {
		0% {
			color: #000000;
		}

		10% {
			color: #FFFFFF;
		}

		20% {
			color: #000000;
		}

		70% {
			color: #000000;
		}

		80% {
			color: #FFFFFF;
		}

		90% {
			color: #000000;
		}

		100% {
			color: #000000;
		}
	}

	@keyframes blink3 {
		0% {
			color: #000000;
		}

		10% {
			color: #000000;
		}

		20% {
			color: #FFFFFF;
		}

		30% {
			color: #000000;
		}

		40% {
			color: #000000;
		}

		50% {
			color: #000000;
		}

		60% {
			color: #000000;
		}

		70% {
			color: #FFFFFF;
		}

		80% {
			color: #000000;
		}

		100% {
			color: #000000;
		}
	}

	@keyframes blink4 {
		0% {
			color: #000000;
		}

		20% {
			color: #000000;
		}

		40% {
			color: #000000;
		}

		30% {
			color: #FFFFFF;
		}

		50% {
			color: #000000;
		}

		60% {
			color: #FFFFFF;
		}

		70% {
			color: #000000;
		}

		100% {
			color: #000000;
		}
	}

	@keyframes blink5 {
		0% {
			color: #000000;
		}

		30% {
			color: #000000;
		}

		40% {
			color: #FFFFFF;
		}

		50% {
			color: #FFFFFF;
		}

		60% {
			color: #000000;
		}

		100% {
			color: #000000;
		}
	}

	.imageChangeBox {
		width: 100px;
		height: 100px;
		background-color: #FFFFFF;
	}

	.emptyCircle {
		position: absolute;
		top: 35.4vh;
		height: 3vh;
		width: 6vw;
	}

	.blueCircle {
		position: absolute;
		top: 35.4vh;
		height: 2.8vh;
		width: 5vw;
	}

	.options {
		position: absolute;
		border: 1px solid #000000;
		background-color: #FFFFFF;
		border-radius: 20px;
	}

	@keyframes colorchange {
		0% {
			background-color: #DF3B1B;
		}

		15% {
			background-color: #EFB211;
		}

		30% {
			background-color: #EFEE11;
		}

		45% {
			background-color: #7AEF11;
		}

		60% {
			background-color: #11EFD2;
		}

		75% {
			background-color: #115DEF;
		}

		90% {
			background-color: #7311EF;
		}

		100% {
			background-color: #EF11C3;
		}
	}

	,
	.footer {
		position: fixed;
		bottom: -10px;
		width: 100%;
		height: 70px;
		border-radius: 15px;
		background-color: #EA7E19;
		border: 1px solid #000000;
	}

	.post {
		height: 120px;
		width: 80vw;
		margin-left: 30px;
		border-radius: 10px;
	}

	.header {
		margin-left: 70px;
		background-color: white;
		border: 1px solid black;
		border-radius: 10px;
	}

	.load {
		position: fixed;
		left: 0;
		top: 0;
		background: white;
		width: 100%;
		height: 100vh;
		animation: backgroundChange .5s linear 3s forwards, fadeOut .5s linear 5s forwards
	}

	.load-text {
		left: 50%;
		top: 45%;
		transform: translate(-50%, -50%);
		position: absolute;
		font-size: 10vmax;
		width: fit-content;
		display: flex;
		animation: colorChange .5s linear 3s forwards;
	}

	.loaded-text {
		transform: scale(1.5);
		animation: scaleText .5s linear .5s forwards;
	}

	.loading-text {
		width: 0;
		overflow: hidden;
		animation: expand .5s linear 1.5s forwards;
	}


	@keyframes scaleText {
		from {
			transform: scale(1.5)
		}

		to {
			transform: scale(1)
		}
	}

	@keyframes expand {
		from {
			width: 0;
		}

		to {
			width: 23vmax;
		}
	}

	@keyframes backgroundChange {
		from {
			background-color: white
		}

		to {
			background-color: pink
		}
	}

	@keyframes colorChange {
		from {
			color: pink
		}

		to {
			color: white
		}
	}

	@keyframes fadeOut {
		from {
			opacity: 1
		}

		to {
			opacity: 0;
			z-index: -1;
		}
	}
	
</style>
