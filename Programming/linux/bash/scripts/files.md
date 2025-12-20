#linux #bash #scripts

```bash
#!/bin/bash

# ./files.sh [-l location][--location location][-e extension][--extension][-s][--stats][-h][--help]

function usage(){	#функция-помощник в использование скрипта
	echo "USAGE: $0 [-l location][--location location][-e extension][--extension][-s][--stats][-h][--help]"
	echo "Examples :"
	echo "$0 -l /etc/ -e txt -s"
	echo "$0 -e img --stats"
	echo
	exit 1
}

LOC_SET=0  	#0-путь к файлам не установлен,1-установлен
STATS=0		#0-не показывать статистику,1-показать статистику

while [ $# -gt 0 ]
do
	case $1 in
		-l|--location )
			LOCATION="$2"
			if ! [ -d "$LOCATION" ]; then
				usage
			fi
			LOC_SET=1
			shift
			shift
			;;

		-e|--extension )
			EXT="$2"
			shift
			shift
			;;

		-s|-stats	)
			STATS=1
			shift
			;;

		-h|--help )
			shift
			usage
			;;

		* )
			usage
			;;
	esac
done

if [ $LOC_SET -ne 1 ]; then
	LOCATION=$(pwd)
fi

echo "Location: $LOCATION"	

# получаем файлы по расширению
if [ "$EXT" != "" ]; then
	ls -l $LOCATION | awk '/^-/' | grep "\.$EXT$" &>/dev/null
	if [ $? -ne 0 ]; then
		echo "Файлы с разрешением: $EXT не найдены"
		exit 2
	fi

	# подсчитываем общий размер файлов(а также min и max)
	ls -l $LOCATION | awk '/^-/' | grep "\.$EXT$" | awk -v stats=$STATS -f /home/roomaxa/project/bash/size.awk
else
	ls -l $LOCATION | awk '/^-/' | awk -v stats=$STATS -f /home/roomaxa/project/bash/size.awk	
fi

44783536
```