# Panduan Instalasi Tema CMS IBI ‘Metrocms’

## OUTLINE
- [Overview] (#overview) 
- [Instalasi Tema] (#instalasi-tema)
- [State Dashboard] (#state-dashboard)
- [Tiles] (#tiles)
- [SlidePanel] (#slide-panel)
- [CSS style] (#css-style)
- [Icon class] (#icon-class)
- [Credits] (#credits)

## OVERVIEW
Metrocms Dashboard (selanjutnya disebut dashboard) pada dasarnya adalah satu halaman khusus yang disediakan untuk menampung link-link aplikasi CMS yang sebenarnya. Ketika digabungkan sebagai tema YiiFramework, dashboard ini memerlukan satu route khusus yang telah dibuat pada `MetroController.php` yaitu `http://contoh-aplikasi.com/index.php?r=metro`. Dari satu route tersebut, aplikasi dapat menampilkan halaman dari link-link yang berbentuk Tiles (kotak-kotak). Namun, pergantian halaman tersebut tidak mengakibatkan pergantian route, karena dashboard memiliki fitur `state` sehingga yang berubah adalah `state` dari dashboard. 

Masing-masing `state` memiliki URL sumber, yaitu URL aplikasi sebenarnya yang kemudian ditampilkan dalam iframe ketika `state` diaktifkan. `state` akan diaktifkan ketika salah satu Tiles diklik oleh user. Maka, agar Tiles dapat diklik, `state` harus diasosiasikan dengan Tiles. Ibaratnya, Tiles adalah elemen anchor `<a>` sedangkan `state` adalah link yang ingin dituju. Berikut perbandingannya

HTML Link

    <a href=“http://contoh-aplikasi.com/index.php?r=admin/semuaSurat”>semua surat</a>

*State Dashboard dalam Tile*

    <div class=“live-tile blue” ui-sref=“namastate”> … </div>

perhatikan link menggunakan atribut `href` yang merujuk pada URL, sedangkan Tile menggunakan atribut `ui-sref` yang merujuk pada nama state yang diinginkan. 

*Misal Tile berikut ini:*

    <div class=“live-tile orange” ui-sref=“semuasurat”> … </div>

Pada file konfigurasi state, terdapat baris yang mendefinisikan bahwa state `semuasurat` memiliki URL sumber, misal http://contoh-aplikasi.com/index.php?r=admin/semuaSurat. Jadi, ketika user mengeklik Tile di atas, maka state `semuasurat` akan diaktifkan, kemudian URL sumbernya, yaitu `http://contoh-aplikasi.com/index.php?r=admin/semuaSurat` akan diload dalam iframe. Pada bab lain akan dijelaskan cara mengonfigurasikan/menambah `state` baru.


## INSTALASI TEMA
 1. Copy folder `metrocms` dari build.cms.ibi ke ‘WebRoot/projectname/themes’
 2. Aktifkan Tema dengan menambah definisi ke ‘WebRoot/projectname/protected/config/main.php

        // autoloading model and component classes
	    'import'=>array(
	    	   'application.models.*',
	    	   'application.components.*',
            ),
	     'theme'=> 'metrocms', // baris ini

 3. Copy dan paste `MetroController.php` dan `SiteController.php` ke ‘WebRoot/projectname/protected/controllers’, replace exisiting file. 

      File `MetroController.php` dipasang agar route ‘index.php?r=metro’ dapat diakses, route ini merupakan halaman dashboard utama metro.
 4. Copy dan paste `contoh.php` dan `login.php`

**catatan**: *Semua layout berubah ketika tema dipasang (termasuk tampilan global), apabila ingin menggunakan layout Anda pada tema metro ini, buka folder ‘themes/metrocms/views/layouts/‘ kemudian ganti `main.php` dengan `main.php` milik Anda yang ada di ‘protected/views/layouts/main.php`. Disarankan untuk menggunakan tema yang sudah disesuaikan agar tampilan login sesuai desain login metro. Juga agar dapat memanfaatkan CSS styling metro untuk beberapa elemen seperti grid, tombol, form, icon, dll*


## STATE DASHBOARD
`State` merupakan layer routing khusus dalam halaman utama dashboard, `state` bekerja seperti URL, bila dalam routing yii kita menggunakan URL misal http://app.com/index.php?r=site/login, pada dashboard navigasi dilakukan dengan merujuk pada state, misal ‘home’, ‘suratMasuk’, dll. URL sumber yang sebenarnya dibungkus dalam state. 

### Bagaimana states bekerja?
Seperti yang saya katakan, `state` membungkus URL yang sebenarnya dari aplikasi cms ini. Secara singkat setiap `state` memiliki URL sumber dibungkus yang merupakan URL aplikasi cms. Ketika sebuah `state` diaktifkan melalui link, maka URL sumber yang dibungkus akan ditampilkan dalam iframe. 

Misal:
 - state `suratMasuk` memiliki URL sumber `http://cms.com/index.php?r=admin/suratMasuk`
 - state `suratKeluar` memiliki URL sumber `http://cms.com/index.php?r=admin/suratKeluar`
 - dll


Sehingga nanti pada dashboard, kita cukup merujuk ke state yang kita inginkan tidak perlu merujuk URL. Pada sub bab selanjutnya akan dijelaskan cara mengonfigurasikan masing-masing state dan URLnya.

### Konfigurasi states
File konfigurasi state ada pada ‘themes/metrocms/views/metro/deps/main.js’. Untuk memulai, buka file tersebut dengan text editor.  Anda akan menemukan baris berikut ini:

    	// Tambah State Baru di sini, hilangkan komen
	// State namastate

	// Hilangkan komen mulai dari baris bawah ini
	// .state('namastate', {
	// 	url: '',
	// 	views: {
	// 		'homeView': {templateUrl: 'themes/metrocms/views/metro/deps/views/home.php', controller: 'HomeCtrl'},
	// 		'pageView': {
	// 			templateUrl: 'themes/metrocms/views/metro/deps/views/overlay.php',
	// 			controller: ['$scope', 'Overlay', function($scope, Overlay){
	// 				$scope.sumber = 'http://localhost:81/cmskin/page.php';
	// 				Overlay.buka();
	// 			}]
	// 		}
	// 	}

Menambah State:
 1. hilangkan komen sesuai petunjuk pada kode
 2. ubah `.state(‘namastate’, {` sesuai nama state yang diinginkan, misal `.state(‘semuaUser’, {`
 3. selanjutnya ganti URL sumber (URL yg akan ditampilkan pada iframe jika state diaktifkan)

    $scope.sumber = ‘http://app.com/index.php?r=admin/users';

Anda telah selesai menambah satu state baru `semuaUser` dan dapat diaktifkan nanti dengan menaruhnya pada atribut `ui-sref` Windows Tile.

## TILES
Tiles merupakan kotak-kotak yang akan menjadi menu utama pada halaman dashboard. Mark-up dasar sebuah Tile. 

Pada elemen div berikan class “live-tile” agar menjadi sebuah Tile.

### Markup Tile
**Berikut ini merupakan Markup Tile yang dapat diletakan pada file `themes/metrocms/views/metro/deps/views/home.html`**

##### Markup Dasar

      <div class="live-tile">
         <div>depan</div>
         <div>belakang</div>
     </div>

##### Contoh
             <!-- TILE BIASA (EFEK SLIDE)-->
		<div class="live-tile" data-speed="750" data-delay="3000" data-bounce="true">
			<span class="tile-title">TILE BIASA</span>
			<div style="text-align:center"><h1 style="font-size: 90px">1 (dapat diganti img)</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">2</h1></div>
		</div>


### Tile dan State
Tambahkan atribut `ui-sref=“namaState”` pada Tile markup seperti di bawah ini:

    		<div class="live-tile" data-speed="750" data-delay="3000" data-bounce="true" ui-sref=“namaState”>
			<span class="tile-title">TILE BIASA</span>
			<div style="text-align:center"><h1 style="font-size: 90px">1 (dapat diganti img)</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">2</h1></div>
		</div>

### Variasi Tiles
#####  Warna Tile
Tersedia class “orange”, “blue”, “green”,

		<!-- TILE WARNA ORANGE -->
		<div class="live-tile orange" data-speed="750" data-delay="1000" data-bounce="true">
			<span class="tile-title">TILE ORANGE</span>
			<div style="text-align:center"><h1 style="font-size: 90px">ISI</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">5</h1></div>
		</div>

##### Efek Tile
Ubah efek dengan mengubah nilai atribut `data-mode`. Efek yang tersedia antara lain, `slide`, `carousel`, `flip`, dan `flip-list` (ada markup khusus)

		<!-- TILE EFEK FADE (data-mode="fade") -->
		<div class="live-tile" data-speed="750" data-delay="8000" data-bounce="true" data-mode="fade">
			<span class="tile-title">TILE FADE</span>
			<div style="text-align:center"><h1 style="font-size: 90px">5</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">6</h1></div>
		</div>

##### Efek Flip List

		<!-- TILE TIPE FLIP (list-tile) -->
		<div class="list-tile mango">
					<span class="tile-title">FLIP TILE</span>
					<ul class="flip-list fourTiles" data-mode="flip-list" data-delay="2000">
							<li>
									<div><img class="full" src="" alt="1" /></div>
									<div><img class="full" src="" alt="1" /></div>
							</li>
							<li>
									<div><img class="full" src="" alt="2" /></div>
									<div><img class="full" src="" alt="2" /></div>
							</li>
							<li>
									<div><img class="full" src="" alt="3" /></div>
									<div><img class="full" src="" alt="3" /></div>
							</li>
							<li data-direction="horizontal">
									<div><img class="full" src="" alt="4" /></div>
									<div><img class="full" src="" alt="4" /></div>
							</li>
					</ul>
		</div>

#####  Arah Animasi Tile (khusus Efek Slide)
Ubah nilai atribut `data-direction`, tersedia `horizontal` dan `vertical`.

		<!-- TILE EFEK SLIDE DIRECTION HORIZONTAL (data-direction=horizontal) -->
		<div class="live-tile" data-speed="750" data-delay="5000" data-bounce="true" data-direction="horizontal">
			<span class="tile-title">DIRECTION HORIZONTAL</span>
			<div style="text-align:center"><h1 style="font-size: 90px">7</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">8</h1></div>
		</div>

##### Lebar Tile
		<!-- TILE LEBAR 2X (310PX) -->
		<div class="live-tile" data-speed="750" data-delay="3000" data-bounce="true" style="width:310px;">
			<span class="tile-title">TILE WIDTH 310px</span>
			<div style="text-align:center"><h1 style="font-size: 90px">1</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">2</h1></div>
		</div>

##### Kecepatan Pergantian Tile

		<!-- TILE SPEED CEPAT (data-delay : 1000) -->
		<div class="live-tile blue" data-speed="900" data-delay="1000" data-bounce="true">
			<span class="tile-title">TILE CEPAT GANTI</span>
			<div style="text-align:center"><h1 style="font-size: 90px">3</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">4</h1></div>
		</div>
##### Kecepatan Transisi Tile

		<!-- TILE SPEED TRANSISI CEPAT (data-speed : 1500) -->
		<div class="live-tile red" data-speed="200" data-delay="3500" data-bounce="true">
			<span class="tile-title">TILE TRANSISI CEPAT</span>
			<div style="text-align:center"><h1 style="font-size: 90px">5</h1></div>
			<div style="text-align:center"><h1 style="font-size: 90px">6</h1></div>
		</div>


## SLIDE PANEL
Ada 2 jenis slidepanel, yaitu:
 1. terletak pada halaman utama, diaktifkan via Tiles
 2. terletak pada iframe (halaman detail), diaktifkan melalui tombol berlogo windows

### Slidepanel Halaman Utama
Tiles, selain dapat mengaktifkan state, juga dapat diklik untuk mengaktifkan slidepanel. Caranya adalah dengan tidak menyertakan atribut `ui-sref` dan menambahkan class `panel` pada Tile. Perhatikan contoh berikut ini, bila Tile di bawah di klik maka panel akan muncul dari kiri.

    	<div class="live-tile blue panel" data-speed="750" href="#menu1" data-delay="8000" data-bounce="true">
		<span class="tile-title">Slidepanel</span>
		<div style="text-align:center"><h1 style="font-size: 90px">5</h1></div>
		<div style="text-align:center"><h1 style="font-size: 90px">6</h1></div>
	</div>

**Isi Panel**
Letakan markup berikut setelah mark-up di atas, lalu sesuaikan seperlunya

    	<div id="menu1" class="isi-panel">
		<a ui-sref="suratmasuk"><i class="fa fa-2x fa-camera-retro"></i></a><br/><br/>
		<a ui-sref=“namaState”><i class="fa fa-2x fa-fax"></i></a><br/><br/>
		<a ui-sref=“namaState”><i class="fa fa-2x fa-cube"></i></a><br/><br/>
		<a ui-sref=“namaState”><i class="fa fa-2x fa-comment"></i></a><br/><br/>
	</div>

id menu1 telah direferensi oleh Tile di atas melalui atribut href=“#menu1”. Dengan begitu, kita dapat membuat beberapa slidepanel pada halaman utama yang memiliki isi berbeda-beda. 

### Slidepanel pada Halaman Detail
Buka file `themes/metrocms/views/metro/deps/views/overlay.html`. Lalu sesuaikan icon dan state dari menu slide panel yang ada pada baris berikut ini

    <div id="paneldinamis" class="isi-panel">
     <a ui-sref="suratmasuk"><i class="fa fa-2x fa-camera-retro"></i></a><br/><br/>
     <a ui-sref=“namaState”><i class="fa fa-2x fa-fax"></i></a><br/><br/>
     <a ui-sref=“namaState”><i class="fa fa-2x fa-cube"></i></a><br/><br/>
     <a ui-sref=“namaState”><i class="fa fa-2x fa-comment"></i></a><br/><br/>
     </div>

## CSS STYLE
Tema ini memanfaatkan tema bootstrap yaitu bootmetro, style yang disediakan oleh bootmetro dapat dipakai di tema ini. Dokumentasi bootmetro dapat dilihat di sini http://aozora.github.io/bootmetro/docs/docs-css.html

## ICON CLASS
Bootmetro telah menyediakan bermacam icon bertema Windows Metro UI, yang dapat dipelajari di sini http://aozora.github.io/bootmetro/docs/docs-icons.html

## CREDITS

 - Jquery (http://jquery.com)
 - AngularJS (http://angularjs.org)
 - AngularUI Router (http://angular-ui.github.io/ui-router)
 - MetroJS (http://www.drewgreenwell.com/projects/metrojs)
 - BootMetro (http://aozora.github.io/bootmetro)
 - PanoramaJS (https://github.com/grohman/js-panorama)
 - pageSlide (https://github.com/srobbin/jquery-pageslide)
