config:
	@mkdir -p IN OUT CONFIG

makemkv:
	@docker run -d --rm \
		--name=makemkv \
		-p 5800:5800 \
		-v ${PWD}/CONFIG:/config:rw \
		-v ${PWD}/IN:/IN:ro \
		-v ${PWD}/OUT:/OUT:rw \
		jlesage/makemkv
	@sleep 5
	@firefox http://127.0.0.1:5800

clean:
	@rm -rf IN OUT CONFIG
