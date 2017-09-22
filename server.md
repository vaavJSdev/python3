
```
#!C:/Python36/python.exe
from http.server import BaseHTTPRequestHandler, HTTPServer
from urllib.parse import urlparse
from urllib import response
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

		result = {"parse":parsePath, "path":filePath , "query": fileQuery}
		return result

	def do_GET(self):
		rootdir = "c:/xampp/htdocs/"
		try:
			path = self.path

			pathParse = self.doPathParse(path)

			filePath = pathParse["path"]
			fileQuery = pathParse["query"]
			file = open(rootdir + filePath)

			fileContent = file.read()
			self._set_headers()
			# WFILE => Contains the output stream for writing a response back to the client.
			self.wfile.write(bytes(fileContent,"UTF-8"))
		except IOError:
			self.send_error(404, 'file not found')

	def do_HEAD(self):
		self._set_headers()

	def do_POST(self):
		path = self.path
		# <--- Gets the size of data
		content_length = int(self.headers['Content-Length'])
		# <--- Gets the data itself
		post_data = self.rfile.read(content_length)
		# rfile Contains an input stream, positioned at the start of the optional input data.
		self._set_headers()
		self.wfile.write(post_data)

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
