```
#!C:/Python36/python.exe
from http.server import BaseHTTPRequestHandler, HTTPServer
import urllib
from urllib.parse import *

# BHRH --> handle the HTTP requests that arrive at the server (GET / POST)
# HTTPServer -->  This class builds on the TCPServer create
# server address as instance variables named server_name and server_port.

# HTTPRequestHandler class
class HTTPServer_RequestHandler(BaseHTTPRequestHandler):

	def _set_headers(self):
		self.send_response(200)
		self.send_header('Content-type', 'text/html')
		self.end_headers()

	def doPathParse(self,path):
		parsePath = urlparse(path)
		filePath = parsePath.path
		fileQuery = parsePath.query
		fileDecode = urllib.parse.parse_qs(fileQuery)
		result = {"parse":parsePath, "path":filePath , "query": fileQuery, "decodeQuery": fileDecode}
		return result

	def doPrintPostAll(self,post_data):
		for key, value in post_data.items():
			self.wfile.write(bytes(key+" = ","UTF-8"))

			for data in value:
				self.wfile.write(bytes(data,"UTF-8"))

	def doPrintPostSpecified(self,key,post_data):
		for data in post_data[key]:
			self.wfile.write(bytes(data,"UTF-8"))

	def doPrintGetAll(self,get_data):
		for key, value in get_data.items():
			self.wfile.write(bytes(key+" = ","UTF-8"))

			for data in value:
				self.wfile.write(bytes(data,"UTF-8"))

	def doPrintGetSpecified(self,key,get_data):
		for data in get_data[key]:
			self.wfile.write(bytes(data,"UTF-8"))

	def do_GET(self):
		rootdir = "c:/xampp/htdocs/"
		try:
			path = self.path

			pathParse = self.doPathParse(path)

			filePath = pathParse["path"]

			fileQuery = pathParse["query"]

			fileDecode = pathParse["decodeQuery"]

			file = open(rootdir + filePath)

			fileContent = file.read()

			self._set_headers()
			# WFILE => Contains the output stream for writing a response back to the client.
			self.wfile.write(bytes(fileContent,"UTF-8"))
			# --- METER EN UNA CLASE --- #
			#self.doPrintGetSpecified("getParam",fileDecode)


		except IOError:
			self.send_error(404, 'file not found')

	def do_HEAD(self):
		self._set_headers()

	def do_POST(self):
		path = self.path
		# <--- Gets the size of data
		content_length = int(self.headers['Content-Length'])
		# <--- Gets the data itself
		# Usamos decode para eliminar el b' del tipo binary
		# Usamos parse_qs para generar un diccionario con los datos POSTeados
		post_data = urllib.parse.parse_qs(self.rfile.read(content_length).decode('utf-8'))
		# rfile Contains an input stream, positioned at the start of the optional input data.
		self._set_headers()
		self.wfile.write(bytes("<p>Hola</p>","UTF-8"))

		# --- METER EN UNA CLASE --- #
		#self.doPrintPostSpecified("postParam",post_data)
		

def run(server_class=HTTPServer, handler_class=HTTPServer_RequestHandler):
	print('starting server...')
	# Server settings
	# Choose port 8080, for port 80, which is normally used for a http server, you need root access
	server_address = ('localhost', 8081)
	httpd = server_class(server_address, HTTPServer_RequestHandler)
	print('running server...')
	httpd.serve_forever()

if __name__ == '__main__':
	run()
```
