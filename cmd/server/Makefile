THIS_FILE := $(lastword $(MAKEFILE_LIST))

.PHONY: migrations_init_test
migrations_init_test:
	mysql -uroot -e "DROP DATABASE IF EXISTS sa_server_test; CREATE DATABASE sa_server_test;" && \
	mysql -uroot sa_server_test < ../../sql/schema.sql
	sql-migrate up -config=migrations.yaml -env=test

.PHONY: migrations_up
migrations_up:
	sql-migrate up -config=migrations.yaml -env=development

.PHONY: migrations_down
migrations_down:
	sql-migrate down -config=migrations.yaml -env=development

.PHONY: migrations_redo
migrations_redo:
	sql-migrate redo -config=migrations.yaml -env=development

.PHONY: migrations_new
migrations_new:
	$(eval PATH := "../../migrations/$(shell date +%s)_$(name).sql")
	@echo "-- +migrate Up\n\n-- +migrate Down\n" > "$(PATH)"
	@echo "$(PATH)"

.PHONY: test
test: migrations_init_test
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../models/acct
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../models/model
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../router
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../handler
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../httperr
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../mware
	SVC_ENV=test SVC_CONF_PATH=$$(pwd) goapp test ../../dbutil

.PHONY: vm_run
vm_run: 
	goapp serve --host=0.0.0.0

.PHONY: vm_migrations_up
vm_migrations_up: 
	sql-migrate up -config=migrations.yaml -env=vagrant
single_test_run: migrations_init_test
	SVC_ENV=test SVC_CONF_PATH=$(CURDIR) goapp test ../../$(test_package) -v -run $(test_name)