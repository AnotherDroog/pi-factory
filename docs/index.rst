.. pi-factory documentation master file, created by
   sphinx-quickstart on Sun Mar 24 11:00:12 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Pi-Factory
================

.. toctree::
   :maxdepth: 4
   :caption: Contents:

   Readme <README.md>
   Manual <MANUAL.md>

The *noma* CLI tool
###################

Details on the CLI interface to Pi-Factory and examples of common usage on lightning nodes

Noma
==================
CLI utility and Python API to manage bitcoin lightning nodes.

Command-line Usage
==================
**node**::

  noma (info|start|stop|restart|logs|check|status)
  noma (temp|swap|ram)
  noma (freq|memory|voltage) [<device>]
  noma usb-setup
  noma tunnel <port> <host>
  noma (backup|restore|source|diff|devtools)
  noma reinstall [--full]

**bitcoind**::

  noma bitcoind (start|stop|info|fastsync|status|check)
  noma bitcoind get <key>
  noma bitcoind set <key> <value>
  noma bitcoind logs [--tail]

**lnd**::

  noma lnd (start|stop|info)
  noma lnd logs [--tail]
  noma lnd connect <address>
  noma lnd (create|unlock|status|check)
  noma lnd lncli [<command>...]
  noma lnd get <key> [<section>] [<path>]
  noma lnd set <key> <value> [<section>] [<path>]
  noma lnd autounlock
  noma lnd autoconnect [<path>]

**noma**::

  noma (-h|--help)
  noma --version

   Options:
  -h --help     Show this screen.
  --version     Show version.

API
###

Our autogenerated Python API documentation

* :mod:`noma.node`
* :mod:`noma.bitcoind`
* :mod:`noma.lnd`
* :mod:`noma.install`
* :mod:`noma.rpcauth`
* :mod:`noma.usb`
