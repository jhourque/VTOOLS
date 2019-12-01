# tree
IN   := ${PWD}/IN
OUT  := ${PWD}/OUT
DONE := ${PWD}/DONE

# ffmpeg parameters
file          ?= ""
in_file       := $(notdir ${file})
out_file      := $(basename ${in_file}).mp4
out_file_hevc := $(basename ${in_file}).hevc

crf         ?= 20
preset      ?= veryfast
scale_opt   := "-vf scale=4480:2240"
test_opt    := "-t 2"
ffmpeg_opts := "-y -map 0:a? -c:a aac -b:a 128k -strict -2 -map 0:v -c:v libx265 -preset ${preset} -x265-params crf=${crf} -c:s copy"
ffmpeg_opts2 := "-y -vf scale=4480:2240 -map 0:a? -c:a aac -b:a 128k -strict -2 -map 0:v -c:v libx265 -preset ${preset} -x265-params crf=${crf} -c:s copy"

#### Rules ####
config:
	@mkdir -p IN OUT DONE

fixname:
	@rename "s/ /_/g" ${IN}/*
	@rename "s/\!//g" ${IN}/*
	@rename "s/,/_/g" ${IN}/*
	@rename "s/\(/_/g" ${IN}/*
	@rename "s/\)/_/g" ${IN}/*
	@rename "s/\& */_/g" ${IN}/*
	@rename "s/\+ */_/g" ${IN}/*
	@rename "s/__/_/g" ${IN}/*
	@ls ${IN}

encode:
	@docker run --rm -v ${IN}:/IN -v ${OUT}:/OUT jrottenberg/ffmpeg:snapshot-ubuntu \
            -i /IN/${in_file} \
            -stats "${ffmpeg_opts}" /OUT/${out_file}

encode_resize:
	@docker run --rm -v ${IN}:/IN -v ${OUT}:/OUT jrottenberg/ffmpeg:snapshot-ubuntu \
            -i /IN/${in_file} \
            -stats "${ffmpeg_opts2}" /OUT/${out_file}

loop_encode:
	@for i in ${IN}/*; \
    do \
        if [ "$$(make get_width file=$${i})" -gt "4480" ]; \
        then make -s encode_resize file=$${i}; \
        else make -s encode file=$${i}; \
        fi; \
        mv $${i} ${DONE}; \
    done

info:
	@echo ${in_file}
	@echo ${out_file}

get_duration:
	@docker run --rm -v ${IN}:/IN --entrypoint ffprobe jrottenberg/ffmpeg -v error -select_streams v:0 -show_entries stream=duration -sexagesimal -of csv=p=0 /IN/${in_file} 

get_size:
	@docker run --rm -v ${IN}:/IN --entrypoint ffprobe jrottenberg/ffmpeg -v error -select_streams v:0 -show_entries stream=width,height -of csv=p=0 /IN/${in_file} 

get_width:
	@docker run --rm -v ${IN}:/IN --entrypoint ffprobe jrottenberg/ffmpeg -v error -select_streams v:0 -show_entries stream=width -of csv=p=0 /IN/${in_file} 

get_height:
	@docker run --rm -v ${IN}:/IN --entrypoint ffprobe jrottenberg/ffmpeg -v error -select_streams v:0 -show_entries stream=height -of csv=p=0 /IN/${in_file} 

check_size:
	@if [ "$$(make get_width file=${file})" -gt "4480" ]; \
    then echo "high"; \
    else echo "low"; \
    fi

update_ffmpeg:
	@docker rmi jrottenberg/ffmpeg:latest
	@docker pull jrottenberg/ffmpeg

x265_test:
	@ffmpeg -loglevel warning -i ${IN}/test-1080p.mkv  -f yuv4mpegpipe - | ./x265  --y4m --crf 20 --preset medium - -o ${OUT}/test-1080p.hevc

loop_hq_hevc:
	@for i in ${IN}/*; \
    do \
        make -s encode_hq_hevc file=$${i}; \
        mv $${i} ${DONE}; \
    done

encode_hq_hevc:
	@echo "ffmpeg -loglevel warning -i ${IN}/${in_file} -f yuv4mpegpipe - | ./x265  --y4m --crf ${crf} -D 10 --preset ${preset} - -o ${OUT}/${out_file_hevc}"
	@ffmpeg -loglevel warning -i ${IN}/${in_file} -f yuv4mpegpipe - | ./x265  --y4m --crf ${crf} -D 10 --preset ${preset} - -o ${OUT}/${out_file_hevc}
