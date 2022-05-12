importera  os
importtid  _
importera  slumpmässigt

importera  pygame
från  pygame . lokalbefolkningen  importerar  *


def  load_image ( file_name , convert_alpha = False ):
	bild  =  pygame . bild . ladda ( f'images/ { file_name } ' )
	om  convert_alpha :
		bild . set_colorkey ( bild . get_at (( 0 , 0 )))
		bild . konvertera_alfa ()

	returnera  bilden


def  get_neighbours ( sladd , ignore_four ):
	för  delta_y , delta_x  i [( - 1 , 0 ), ( 0 , - 1 ), ( 0 , 1 ), ( 1 , 0 )]:
		ny_y , ny_x  =  sladd [ 0 ] +  delta_y , sladd [ 1 ] +  delta_x
		om (
				0  <=  new_y  <  len ( brickor ) och
				0  <=  ny_x  <  len ( brickor [ 0 ]) och
				brickor [ new_y ][ new_x ] !=  3  och
				( ignorera_fyra  eller  brickor [ new_y ][ new_x ] !=  4 )
		):
			yield  new_y , new_x


def  get_shorttest_path ( sökvägar , slut , sett = Ingen , ignore_four = Falskt ):
	om  den ses  är  ingen :
		sett  =  set ()

	nya_vägar  = []
	för  väg  i  stigar :
		för  granne  i  get_neighbours ( sökväg [ - 1 ], ignore_four ):
			om  grannen  syns : _ 
				Fortsätta
			om  granne  ==  slut :
				returväg  + [ granne ] _ 
			nya_vägar . lägg till ( sökväg  + [ granne ])
			sett . lägga till ( granne )

	om  inte  nya_vägar :
		returnera  Falskt
	return  get_shorttest_path ( nya_vägar , slut , sett , ignorera_fyra )


klass  Entitet ( pygame . sprite . Sprite ):

	def  __init__ ( själv , mål = Inget ):
		själv . mål  =  mål

		själv . vänd  =  'R'
		själv . next_facing  =  Ingen
		själv . hastighet  =  4   # px
		själv . vägg  = { 3 , 4 }
		själv . död  =  Falskt
		själv . god_mode  =  Falskt
		själv . god_mode_till  =  Ingen
		själv . rädd  =  Falskt
		själv . spawn_tile  = ( 10 , 11 )

		själv . bilduppsättningar  = {}
		själv . facing_to_images  =  Ingen
		själv . bilder  =  Inga
		själv . bild  =  Ingen
		själv . rect  =  Ingen
		själv . rendered_first_cycle  =  Falskt
		själv . fast  =  Falskt

		själv . frames_per_image  =  6
		själv . frame_idx  =  0

		själv . n_images  =  Inga
		själv . image_idx  =  0

	def  add_images ( self , name , file_name , n_images , image_order = None ):
		raw_images  =  load_image ( filnamn , True )

		* _ , bredd , höjd  =  råa_bilder . get_rect ()
		gap  = ( bredd  -  höjd  *  n_bilder ) // ( n_bilder  -  1 )

		bilder  = [
			råa_bilder . underytan (( höjd  +  gap ) *  idx , 0 , höjd , höjd )
			för  idx  i  intervall ( n_images )
		]
		if  image_order :
			image_frames  =  len ( bilder ) //  len ( image_order )
			vända_till_bilder  = {
				riktning : bilder [ idx : idx + image_frames ]
				för  riktning , idx  i  zip (
					image_order , range ( 0 , n_images , n_images  //  len ( image_order ))
				)
			}
		annat :
			bildramar  =  len ( bilder )
			facing_to_images  =  Ingen

		själv . bilduppsättningar [ namn ] =  bilder , vända_till_bilder , bildramar

	def  set_images ( själv , namn ):
		själv . bilder , själv . vända_mot_bilder , själv . n_images  =  själv . bilduppsättningar [ namn ]
		själv . bild  =  jag . bilder [ 0 ]
		själv . frame_idx  =  0
		själv . image_idx  =  0
		själv . rendered_first_cycle  =  Falskt

	def  set_rect ( self , x_tile , y_tile ):
		själv . rect  =  själv . bild . get_rect ()
		själv . rät . mitten  = (
			TILE_WIDTH  *  x_tile  +  22 ,   # kartkant har 22px
			TILE_HEIGHT  *  y_tile  +  22 ,
		)

	def  draw ( själv , yta ):
		om  inte  själv . fast  eller  själv . död :
			själv . frame_idx  +=  1
		om  jag själv . frame_idx  ==  själv . frames_per_image :
			själv . frame_idx  =  0
			själv . image_idx  +=  1

		om  jag själv . image_idx  ==  själv . n_images :
			själv . rendered_first_cycle  =  Sant
			själv . image_idx  =  0

		om  jag själv . vända_till_bilder  är  inte  Ingen :
			själv . bild  =  jag . vända_till_bilder [ själv . vända mot ][ själv . image_idx ]
		annat :
			själv . bild  =  jag . bilder [ själv . image_idx ]

		om  jag själv . rect [ 0 ] +  själv . rect [ 2 ] >=  BREDD :
			själv . rect [ 0 ] +=  själv . rekt [ 2 ] -  BREDD
		elif  själv . räta [ 0 ] <  0 :
			själv . rect [ 0 ] +=  WIDTH  -  self . räta [ 2 ]

		yta . blit ( självbild , själv . rekt ) _ _

	@ fastighet
	def  left_top ( själv ):
		återvända  själv . rect [ 0 ] +  4 , själv . rect [ 1 ] +  4

	@ fastighet
	def  left_top_tile ( själv ):
		x , y  =  själv . left_top
		returnera  y  //  TILE_HEIGHT , x  //  TILE_WIDTH

	@ fastighet
	def  middle_cord ( själv ):
		återvända  själv . rect [ 0 ] +  24 , själv . rect [ 1 ] +  24

	@ fastighet
	def  middle_tile ( själv ):
		x , y  =  själv . middle_cord
		returnera  y  //  TILE_HEIGHT , x  //  TILE_WIDTH

	@ fastighet
	def  right_bottom ( själv ):
		återvända  själv . rect [ 0 ] +  43 , själv . rect [ 1 ] +  43

	@ fastighet
	def  right_bottom_tile ( själv ):
		x , y  =  själv . right_bottom
		returnera  y  //  TILE_HEIGHT , x  //  TILE_WIDTH

	@ fastighet
	def  ignore_four ( själv ):
		returnera  4  inte  i  sig själv . vägg

	def  move_or_turn ( själv ):
		x , y  =  själv . left_top
		om  x  %  TILE_WIDTH  ==  0  och  y  %  TILE_HEIGHT  ==  0 :
			tillgängliga_vägbeskrivningar  = [
				riktning  för  riktning  i [ 'R' , 'L' , 'U' , 'D' ]
				om  jag själv . can_move_towards ( riktning , Sant )
			]
			current_tile  =  själv . middle_tile
			target_tile  =  själv . mål . middle_tile

			om  jag själv . dead  and  current_tile  ==  själv . spawn_tile :
				själv . död  =  Falskt
				själv . rädd  =  Falskt
				själv . set_images ( 'levande' )
				själv . hastighet  =  4

			om  jag själv . död :
				target_tile  =  själv . spawn_tile
				sökväg  =  get_shorttest_path (
					[[ aktuell_bricka ]],
					target_tile ,
					ignore_four = själv . ignore_four ,
				)
				riktning  =  själv . get_direction ( aktuell_bricka , sökväg [ 1 ])
				själv . vänd  =  riktning
			annat :
				om  slumpmässigt . slumpmässig () >  0,2 :
					sökväg  =  get_shorttest_path (
						[[ aktuell_bricka ]],
						target_tile ,
						ignore_four = själv . ignore_four ,
					)
					om  sökväg :
						riktning  =  själv . get_direction ( aktuell_bricka , sökväg [ 1 ])
						om  inte  själv . rädd :
							själv . vänd  =  riktning
						annat :
							tillgängliga_riktningar . ta bort ( riktning )
							själv . vända  =  slumpmässigt . val ( tillgängliga_riktningar )
					annat :
						själv . vända  =  slumpmässigt . val ( tillgängliga_riktningar )
				annat :
					själv . vända  =  slumpmässigt . val ( tillgängliga_riktningar )

		själv . flytta ()

	@ statisk metod
	def  get_direction ( tile_from , tile_to ):
		om  tille_from [ 0 ] <  tile_to [ 0 ]:
			returnera  'D'
		elif  tile_from [ 0 ] >  tile_to [ 0 ]:
			returnera  "U"
		elif  tile_from [ 1 ] <  tile_to [ 1 ]:
			returnera  'R'
		elif  tile_from [ 1 ] >  tile_to [ 1 ]:
			returnera  'L'

	def  can_move_towards ( själv , riktning , vändning = Falskt ):
		x , y  =  själv . left_top
		om det  inte  vrids  eller ( y  %  TILE_HEIGHT  ==  0  och  x  %  TILE_WIDTH  ==  0 ):
			om  riktning  ==  'R' :
				y_tile , x_tile  =  själv . left_top_tile
				returnera  brickor [ y_tile ][ x_tile  +  1 ] inte  i sig  själv . vägg
			elif  riktning  ==  'L' :
				y_tile , x_tile  =  själv . right_bottom_tile
				returnera  brickor [ y_tile ][ x_tile-  1  ] inte i  sig  själv . vägg
			elif  direction  ==  'U' :
				y_tile , x_tile  =  själv . right_bottom_tile
				returnera  brickor [ y_tile  -  1 ][ x_tile ] inte  i sig  själv . vägg
			elif  direction  ==  'D' :
				y_tile , x_tile  =  själv . left_top_tile
				returnera  brickor [ y_tile  +  1 ][ x_tile ] inte  i sig  själv . vägg

		returnera  Falskt

	def  flytta ( själv ):
		själv . fast  =  Falskt
		om  inte  själv . can_move_towards ( självvändande ) : _
			själv . fast  =  Sant
			returnera  Falskt

		om  jag själv . vänd  ==  'R' :
			själv . rät . move_ip ( själv . hastighet , 0 )
		elif  själv . vänd  ==  'L' :
			själv . rät . move_ip ( - själv . hastighet , 0 )
		om  jag själv . vänd  ==  'U' :
			själv . rät . move_ip ( 0 , - själv . hastighet )
		elif  själv . vänd  ==  'D' :
			själv . rät . move_ip ( 0 , själv . hastighet )

	def  does_collide ( själv , sprite ):
		returnera  pygame . sprite . kollidera_mask ( själv , sprite )


klass  SmallDot ( pygame . sprite . Sprite ):

	def  __init__ ( själv ):
		själv . image  =  load_image ( 'dot.png' , True ). under ytan ( 0 , 0 , 30 , 30 )
		själv . rect  =  själv . bild . get_rect ()
		själv . rät . center  = ( 45 , 45 )

	def  draw ( själv , yta , sladd ):
		själv . rät . centrum  =  sladd
		yta . blit ( självbild , själv . rekt ) _ _


klass  BigDot ( pygame . sprite . Sprite ):

	def  __init__ ( själv ):
		själv . image  =  load_image ( 'dot.png' , True ). under ytan ( 30 , 0 , 40 , 30 )
		själv . rect  =  själv . bild . get_rect ()
		själv . rät . center  = ( 45 , 45 )

		själv . frames_per_image  =  30
		själv . frame_idx  =  0
		själv . show_image  =  Sant

	def  draw ( själv , yta , sladd ):
		själv . frame_idx  +=  1
		om  jag själv . frame_idx  >  själv . frames_per_image :
			själv . frame_idx  =  0
			själv . show_image  =  inte  själv . visa_bild

		om  jag själv . visa_bild :
			själv . rät . centrum  =  sladd
			yta . blit ( självbild , själv . rekt ) _ _


os . environ [ 'SDL_VIDEO_WINDOW_POS' ] =  '1080,30'
pygame . init ()

map_image  =  load_image ( 'map.png' )
_ , _ , WIDTH , HEIGHT  =  map_image . get_rect ()

display  =  pygame . display . set_mode (( WIDTH , HEIGHT ))
# 0 - tom, 1 - liten_prick, 2 - stor_prick, 3 - vägg, 4 - rosa vägg
brickor  = [
	[ 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 ],
	[ 3 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 3 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 3 ],
	[ 3 , 2 , 3 , 3 , 3 , 1 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 1 , 3 , 3 , 3 , 2 , 3 ],
	[ 3 , 1 , 3 , 3 , 3 , 1 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 1 , 3 , 3 , 3 , 1 , 3 ],
	[ 3 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 3 ],
	[ 3 , 1 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 1 , 3 ],
	[ 3 , 1 , 3 , 3 , 3 , 1 , 3 , 1 , 1 , 1 , 3 , 1 , 1 , 1 , 3 , 1 , 3 , 3 , 3 , 1 , 3 ],
	[ 3 , 1 , 1 , 1 , 1 , 1 , 3 , 3 , 3 , 0 , 3 , 0 , 3 , 3 , 3 , 1 , 1 , 1 , 1 , 1 , 3 ],
	[ 3 , 3 , 3 , 3 , 3 , 1 , 3 , 0 , 0 , 0 , 0 , 0 , 0 , 0 , 3 , 1 , 3 , 3 , 3 , 3 , 3 ],
	[ 3 , 3 , 3 , 3 , 3 , 1 , 3 , 0 , 3 , 4 , 4 , 4 , 3 , 0 , 3 , 1 , 3 , 3 , 3 , 3 , 3 ],
	[ 3 , 3 , 3 , 3 , 3 , 1 , 3 , 0 , 3 , 0 , 0 , 0 , 3 , 0 , 3 , 1 , 3 , 3 , 3 , 3 , 3 ],
	[ 0 , 0 , 0 , 0 , 0 , 1 , 0 , 0 , 3 , 0 , 0 , 0 , 3 , 0 , 0 , 1 , 0 , 0 , 0 , 0 , 0 ],
	[ 3 , 3 , 3 , 3 , 3 , 1 , 3 , 0 , 3 , 3 , 3 , 3 , 3 , 0 , 3 , 1 , 3 , 3 , 3 , 3 , 3 ],
	[ 3 , 3 , 3 , 3 , 3 , 1 , 3 , 0 , 0 , 0 , 0 , 0 , 0 , 0 , 3 , 1 , 3 , 3 , 3 , 3 , 3 ],
	[ 3 , 3 , 3 , 3 , 3 , 1 , 3 , 0 , 3 , 3 , 3 , 3 , 3 , 0 , 3 , 1 , 3 , 3 , 3 , 3 , 3 ],
	[ 3 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 3 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 3 ],
	[ 3 , 1 , 3 , 3 , 3 , 1 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 1 , 3 , 3 , 3 , 1 , 3 ],
	[ 3 , 2 , 1 , 1 , 3 , 1 , 1 , 1 , 1 , 1 , 0 , 1 , 1 , 1 , 1 , 1 , 3 , 1 , 1 , 2 , 3 ],
	[ 3 , 3 , 3 , 1 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 1 , 3 , 3 , 3 ],
	[ 3 , 1 , 1 , 1 , 1 , 1 , 3 , 1 , 1 , 1 , 3 , 1 , 1 , 1 , 3 , 1 , 1 , 1 , 1 , 1 , 3 ],
	[ 3 , 1 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 1 , 3 ],
	[ 3 , 1 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 1 , 3 , 1 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 1 , 3 ],
	[ 3 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 1 , 3 ],
	[ 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 , 3 ],
]
TILE_WIDTH , TILE_HEIGHT  =  WIDTH  //  len ( brickor [ 0 ]), HEIGHT  //  len ( brickor )

FPS  =  60
klocka  =  pygame . tid . Klocka ()

pac_man  =  Entitet ()
pac_man . add_images ( 'alive' , 'pac_man.png' , 8 , ( 'R' , 'U' , 'L' , 'D' ))
pac_man . add_images ( 'death' , 'pac_man_death.png' , 12 )
pac_man . set_images ( 'levande' )
pac_man . set_rect ( 10 , 17 )

fiender  = {}
enemies_names  = [ 'blinky' , 'clyde' , 'inky' , 'pinky' ]
för  enemy_name  i  enemies_names :
	fiender [ fiendenamn ] =  Entitet ( pac_man )
	fiender [ fiendenamn ]. add_images ( 'alive' , f' { enemy_name } .png' , 8 , ( 'R' , 'L' , 'U' , 'D' ))
	fiender [ fiendenamn ]. add_images ( 'scared' , 'ghost_scared.png' , 2 )
	fiender [ fiendenamn ]. add_images ( 'eyes' , 'ghost_eyes.png' , 4 , ( 'R' , 'L' , 'U' , 'D' ))
	fiender [ fiendenamn ]. set_images ( 'levande' )
	fiender [ fiendenamn ]. set_rect ( 10 , 11 )

small_dot  =  SmallDot ()
big_dot  =  BigDot ()

game_start  =  tid . tid ()
ghosts_released  =  0
release_time  =  5   # sekunder

medan  det är sant :
	för  evenemang  i  pygame . händelse . få ():
		om  händelse . typ  ==  AVSLUTA :
			pygame . sluta ()
			avsluta ()
		elif  event . typ  ==  pygame . KEYDOWN :
			om  händelse . nyckel  ==  ord ( 'w' ):
				pac_man . next_facing  =  'U'
			elif  event . nyckel  ==  ord ( 'a' ):
				pac_man . next_facing  =  'L'
			elif  event . nyckel  ==  ord ( 's' ):
				pac_man . next_facing  =  'D'
			elif  event . nyckel  ==  ord ( 'd' ):
				pac_man . next_facing  =  'R'

	display . blit ( map_image , ( 0 , 0 ))

	tid_sedan_start  =  tid . tid () -  game_start
	if  enemies_names  and  time_since_start  //  release_time  +  1  >  ghosts_released :
		ghosts_released  +=  1
		fiender [ fiender_namn . pop ()]. vägg  = { 3 }

	om  pac_man . can_move_towards ( pac_man . next_facing , True ):
		pac_man . vända  =  pac_man . nästa_vända mot
		pac_man . next_facing  =  Ingen

	om  pac_man . god_mode  och  pac_man . god_mode_till  <  tid . tid ():
		pac_man . god_mode  =  Falskt
		pac_man . god_mode_till  =  Ingen
		för  fiende  i  fiender . värden ():
			om  fiende . rädd  och  inte  fiende . död :
				fiende . rädd  =  Falskt
				fiende . set_images ( 'levande' )

	om  inte  pac_man . död :
		pac_man . flytta ()
	pac_man . rita ( visa )

	för  fiende  i  fiender . värden ():
		om  inte  pac_man . död :
			fiende . flytta_eller_svänga ()
		fiende . rita ( visa )
		om  pac_man . does_collide ( fiende ) och  inte  pac_man . död :
			om  inte  fiende . rädd  och  inte  fiende . död :
				pac_man . död  =  Sant
				pac_man . set_images ( 'död' )
				pac_man . frames_per_image  =  12
			elif  inte  fiende . död :
				fiende . död  =  Sant
				fiende . set_images ( 'ögon' )
				fiende . hastighet  =  8
				fiende . rect [ 0 ] -= ( fiende . rect [ 0 ] +  4 ) %  TILE_WIDTH
				fiende . rect [ 1 ] -= ( fiende . rect [ 1 ] +  4 ) %  TILE_HEIGHT

	dots_left  =  Falskt
	för  y , rad  i  enumerate ( brickor ):
		för  x , val  i  enumerate ( rad ):
			om  val  ==  1 :
				dots_left  =  Sant
				sladd  =  x  *  TILE_WIDTH  +  20 , y  *  TILE_HEIGHT  +  20
				liten_prick . rita ( display , sladd )
				om  pac_man . gör_kolliderar ( small_dot ):
					brickor [ y ][ x ] =  0
			om  val  ==  2 :
				dots_left  =  Sant
				sladd  =  x  *  TILE_WIDTH  +  15 , y  *  TILE_HEIGHT  +  20
				big_dot . rita ( display , sladd )
				om  pac_man . gör_kolliderar ( big_dot ):
					brickor [ y ][ x ] =  0
					pac_man . god_mode  =  Sant
					pac_man . god_mode_till  =  tid . tid () +  5
					för  fiende  i  fiender . värden ():
						om  inte  fiende . död :
							fiende . rädd  =  Sant
							fiende . set_images ( 'rädd' )

	om  dots_left  och ( inte  pac_man . dead  eller  inte  pac_man . rendered_first_cycle ):
		pygame . display . uppdatera ()
		klocka . bock ( FPS )